# Introduction to Hail


This document gives an overview of the Hail architecture and source
code.

## Background

Hail source code is stored in a monolithic repository (monorepo) on
GitHub at hail-is/hail.  $HAIL will denote the repository root below.
Hail is open source and developed in the open.

Hail is written in Python, Java/Scala (both JVM langauges), and C/C++.

Hail has two user-facing components: Hail Query and Hail Batch.  Both
provide Python interfaces.  Hail Query is for distributed, out-of-core
manipulation and analysis of tabular and genomic data.  Hail Batch is
for the execution of graphs containers.

The Hail client libraries are deployed in the Python Package Index
(PyPI) hail package.  The Hail package exposes two Python modules:
`hail` and `hailtop`.  `hail` provides the Hail Query interface.
`hailtop.batch` provides the batch interface.  `hailtop` contains
various other infrastructure submodules (see below).

The hail package also contains the `hailctl` command line utility that
provides a number of features, including managing Google Dataproc (a
hosted Spark service), Hail service authorization, Hail configuration,
interacting with the Hail developer tools, and managing the Hail Batch
service.  `hailctl` is implemented in the `hailtop.hailctl` submodule.

Users can run Hail on their own system, or on the Hail service that is
operated by the Hail team.  The Hail service is implemented by a
collection of microservices that are deployed on Kubernetes (K8s) and
Google Cloud Platform (GCP).  We refer to K8s and GCP as our Virtual
Datacenter (VDC).

## Hail Query

The `hail` module is implemented in $HAIL/hail/python/hail, this is the Hail Query Python interface.
The Hail Query interface is lazy: executing a pipeline builds an intermediate representation
representing the query.  The IR is implemented in the `hail.ir` submodule.  When a query is ready to
be executed, it is sent to a backend, implemented in `hail.backend`.  There are three backends:
SparkBackend, LocalBackend and ServiceBackend.  At the time of writing, the SparkBackend is complete
and the ServiceBackend is mostly complete (it is missing a PCA implementation with clear error
bounds and some block matrix operations do not scale). The LocalBackend has languished.

The Spark backend works as follows.  The IR is serialized and sent to
a JVM child process via [py4j](https://www.py4j.org/).  The entrypoint
for this is the Scala class is.hail.backend.spark.SparkBackend.  The
SparkBackend parses the IR, runs a query optimizer on it, generates
custom JVM bytecode for the query which is called from a Spark
computational graph, which it then submits to Spark for execution.
The result is then returned via py4j back to Python and the user.

The ServiceBackend is structured differently.  The IR is again
serialized, but is written to cloud storage and a job is submitted
to Hail Batch to execute the aforementioned IR.

The local backend is similar to the Spark backend, except the
execution is performed on the local machine in the JVM instead of
being submitted to Spark.

## Hail Batch

A batch is a graph of jobs with dependencies.  A job includes
configuration for a container to execute, including image, inputs,
outputs, command line, environment variables, etc.  The Hail Batch
Python interface provides and high-level interface for constructing
batches of jobs.  A batch can then be submitted to a backend for
execution.  There are two backends: LocalBackend and ServiceBackend.

The local backend executes the batch on the local machine.

The service backend serializes the batch as JSON and submits it to the
batch microservice at https://batch.hail.is/.  The Batch service then
executes the graph on a fleet of GCP virtual machines called workers.

## Source Code Organization

Here are some key source code locations:

Hail Query:

* $HAIL/hail/python/hail: the Hail Query Python interface
* $HAIL/hail/src: the Hail Query Java/Scala code

Hail Batch:

* $HAIL/batch: the batch service
* $HAIL/benchmark: Hail Query benchmarking tools
* $HAIL/hail/python/hailtop/batch: Python Batch interface
* $HAIL/hail/python/hailtop/batch_client: low-level Batch client library

Services (see below for descriptions):

* $HAIL/auth
* $HAIL/batch
* $HAIL/ci
* $HAIL/gateway
* $HAIL/internal-gateway
* $HAIL/website

Libraries for services:

* $HAIL/gear: gear services library
* $HAIL/hail/python/hailtop/aiocloud: asyncio client libraries for AWS, GCP, and Azure
* $HAIL/hail/python/hailtop/auth: user authorization library
* $HAIL/hail/python/hailtop/config: user and deployment configuration library
* $HAIL/hail/python/hailtop/tls.py: TLS utilities for services
* $HAIL/hail/python/hailtop/utils: various
* $HAIL/tls: for TLS configuration and deployment
* $HAIL/web_common: service HTML templates

Other:

* $HAIL/blog: blog configuration
* $HAIL/build.yaml: the Hail build, test and deployment configuration
* $HAIL/datasets: ETL code for the Hail Query Datasets API
* $HAIL/docker: base docker images used by services
* $HAIL/hail/python/hailtop/hailctl: implementation of the hailctl command-line tool
* $HAIL/ukbb-rg: UKBB genetic correlation browser configuration, available at https://ukbb-rg.hail.is/

## Hail Query Java/Scala Code Organization

This section is not complete.

* is.hail.annotation: For historical reasons, a Hail Query runtime
  value (e.g. an int, array, string, etc.) is called an annotation.
  In the JVM, there are two representations of runtime values: as JVM
  objects or as a pointer to explicitly managed memory off the Java
  heap called a region value.  Annotation also sometimes refer to just
  the JVM object representation.  Explicitly managed off-(Java-)heap
  values are also referred to as "unsafe".

* is.hail.asm4s: The Hail Query optimizer generates JVM bytecode to
  implement queries.  asm4s is a high-level Scala interface for
  generating JVM bytecode.

* is.hail.lir: lir is a low-level intermediate representation (IR) for
  JVM bytecode.  The high-level asm4s interface is implemented in
  terms of lir.  lir can generate raw JVM bytecode that can be loaded
  into the interpreter and invoked via reflection.

* is.hail.types: Hail Query has several different notions of types.
  For values, there are three kinds of type: virtual, physical and
  encoded types.  Virtual types are usual-visible types like int,
  array and string.  Physical types are implementations of virtual
  types.  For example, arrays might be stored densely or sparsely.
  Encoded types specify how to (de)serialize types for reading and
  writing.  There also higher-level types for Tables, MatrixTables and
  BlockMatrices.

* is.hail.expr: expr is a large, disorganized package that contains
  the Hail Query IR, query optimizer and IR code generator.

* is.hail.io.fs: fs contains an abstract filesystem interface, FS, and
  implementations for Google Storage and Hadoop.  We need to implement
  one for local UNIX filesystems.

* is.hail.io: This includes support for reading and writing a variety
  of file formats.

* is.hail.services: This package is related to the implementation of
  services.  This includes a Java/Scala client for Hail Batch, the
  shuffle service client and server, and support for TLS,
  authentication, deployment configuration, etc.

* is.hail.rvd: The fundamental abstraction in Spark is the resilient
  distributed dataset (RDD).  When Hail generates Spark pipelines to
  implement queries, it generates an RDD of off-(Java-)heap region
  values.  An RVD is a Region Value Dataset, an abstraction for a RDD
  of region values.

* is.hail.backend: each Python backend (Spark, local, service) has a
  corresponding backend in Scala

## Microservice Overview

The Hail service is implemented as a collection of microservices (for
the list, see below).  The services are implemented in Python (mostly)
and Java/Scala (or a mix, using py4j).  The code for the services can
be found at the top-level, e.g. the batch and batch-driver
implementation can be found in $HAIL/batch.  For publicly accessible
services, they are available at https://<service>.hail.is/, e.g. the
batch service is available at https://batch.hail.is/.

Python services are implemented using Python asyncio,
[aiohttp](https://docs.aiohttp.org/en/stable/) and Jinja2 for HTML
templating.

Some services rely on 3rd party services.  Those include:

* ci depends on GitHub

* batch, ci and auth depend on K8s

* batch depends on K8s and GCP

* website depends (client-side) on Algolia for search

Services store state in a managed MySQL Google CloudSQL instance.

There is a collection of libraries to facilitate service development:

* `hailtop.aiogoogle`: asyncio client libraries for Google services.
  There are currently libraries for GCE, GCR, IAM, Stackdriver Logging
  and (in progress) Google Storage

* `hailtop.config`: to manage user and deployment configuration

* `hailtop.auth`: to manage user authorization tokens

* `hailtop.utils`: various

* `gear`: verifying user credentials, CSRF protection, a database
  interface, logging and user session management

* `web_common`: common HTML templates for the services Web UI (except
  site)

* `tls`: infrastructure for encrypting internal communication between
  services

## List of Microservices

* auth and auth-driver: for user authorization, authorization token
  verification and account management

* batch and batch-driver: the Hail Batch service

* ci: We've implemented our own continuous integration and continuous
  deployed (CI/CD) system. It also hosts a developer status board
  at https://ci.hail.is/me.

* gateway: gateway is an nginx reverse proxy that terminates TLS
  connections and forwards requests to services in K8s.  It is
  possible to run multiple copies of the Hail services in K8s, each
  set in a separate K8s namespace.  gateway forwards requests to the
  K8s service in the appropriate namespace.

* internal-gateway: batch workers run on GCE VMs, not in K8s.  The
  internal-gateway is an nginx reverse proxy that terminates
  connections from the Google Virtual Private Cloud (VPC) network and
  connections to the services in K8s.

* website: site implements the main Hail website https://hail.is/
  including the landing page and Hail Query and Hail Batch
  documentation.

There are two types of services: managed and unmanaged.
CI handles deployment of managed services, while unmanaged services
are deployed by hand using their respective Makefiles. The
unmanaged services are gateway and internal-gateway.


## Hail Overview  
  
Hail is a library for analyzing structured tabular and matrix data. Hail
contains a collection of primitives for operating on data in parallel, as well
as a suite of functionality for processing genetic data.

This section of Hail’s documentation contains detailed explanations of Hail’s
architecture, primitives, classes, and libraries.

  * ``Expressions``
    * ``What is an Expression?``
    * ``Boolean Logic``
    * ``Conditional Expressions``
    * ``Missingness``
    * ``Functions``
  * ``Tables``
    * ``Import``
    * ``Global Fields``
    * ``Keys``
    * ``Referencing Fields``
    * ``Updating Fields``
    * ``Aggregation``
    * ``Joins``
    * ``Interacting with Tables Locally``
  * ``MatrixTables``
    * ``Keys``
    * ``Referencing Fields``
    * ``Import``
    * ``Common Operations``
    * ``Aggregation``
    * ``Group-By``
    * ``Joins``
    * ``Interacting with Matrix Tables Locally``

---

## Hail for New Engineers

The Hail team's mission is to accelerate the pace of biomedical research in service of global human
health. We originally focused on the needs of statistical geneticists working with very large human
genetic datasets. These datasets motivated the "Hail Query" project, which we describe later.

## Genetics, Briefly

Hail team is a part of Ben Neale's lab which is part of the Analytical and Translational Genetics
Unit (ATGU) at the Massachusetts General Hospital (MGH) and part of the Stanley Center (SC) at the
Broad Institute (Broad). Ben's lab studies, among other things, [statistical
genetics](https://en.wikipedia.org/wiki/Statistical_genetics) (statgen). The Broad Institute's
self-description, which follows, highlights a different field of study: genomics.

> [The] Broad Institute of MIT and Harvard was launched in 2004 to improve human health by using
> genomics to advance our understanding of the biology and treatment of human disease, and to help
> lay the groundwork for a new generation of therapies.

Genetics and genomics may seem similar to a software engineer's ear; however, genetics is the study
of heredity whereas genomics is the study of the genome.[^1] The [history of
genetics](https://en.wikipedia.org/wiki/History_of_genetics) is deeply intertwined with statistics
which perhaps explains some of the distinction from genomics whose history lies more firmly in
biology.

The history of genetics is also deeply intertwined with
[eugenics](https://en.wikipedia.org/wiki/History_of_eugenics) and
[racism](https://en.wikipedia.org/wiki/Scientific_racism). Sadly, this continues today (see: [James
Watson](https://en.wikipedia.org/wiki/James_Watson)). The team Zulip channel and private messages
are both valid forums for discussing these issues.

The Neale Lab manifests the Broad's credo by studying the relationship between human disease and
human genetics. This is sometimes called "genetic epidemiology". One common
statistical-epidemiological study design is the case-control study. A case-control study involves
two groups of people. The "case" group has been diagnosed with the disease. The "control" group has
not. We collect genetic material from both groups and search for a correlation between the material
and the groups.

There is at least one successful example of genetic studies leading to the development of a drug:
the discovery of PCSK9. In 2013, the New York Times [reported on the
discovery](https://www.nytimes.com/2013/07/10/health/rare-mutation-prompts-race-for-cholesterol-drug.html)
of an association between mutations in the PCSK9 gene and high levels of LDL cholesterol. By 2017,
[further
studies](https://www.nytimes.com/2017/03/17/health/cholesterol-drugs-repatha-amgen-pcsk9-inhibitors.html)
demonstrated *remarkable* reduction in LDL cholesterol levels. Unfortunately, as late as 2020, these
drugs [remain extraordinarily
expensive](https://www.nytimes.com/2018/10/02/health/pcsk9-cholesterol-prices.html).

Hail is also the computational foundation of the [Genome Aggregation
Database](https://gnomad.broadinstitute.org), gnomAD. gnomAD collects a large and diverse set of
human sequences (gnomAD v4 comprised 955,000 individuals) in order to characterize the statistical
distributions of properties of the human genome. Some properties are quite simple, such as the
frequency of a given allele, and some are quite complex such as the
[LOEUF](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7334197/). This information is used every day
by researchers *and* clinicians.

## A Brief History

Around 2015, human genetic datasets had grown so large that analysis on a single computer was
prohibitively time-consuming. Moreover, partitioning the dataset and analyzing each partition
separately necessitated increasingly complex software engineering. We started the Hail project to
re-enable simple and interactive (i.e. fast) analysis of these large datasets.

Hail was a command-line program that used Apache Spark to run analysis on partitioned genetic
datasets simultaneously using hundreds of computer cores. To use Hail, a user needs an Apache Spark
cluster. Most Hail users use Google Dataproc Spark clusters.

The essential feature of a human genetic dataset is a two-dimensional matrix of human
genotypes. Every genotype has the property "number of alternate alleles". This property allows a
matrix of genotypes to be represented as a numeric matrix. Geneticists use linear algebraic
techniques on this numeric matrix to understand the relationship between human disease and human
genetics.

In November of 2016, the Hail command-line interface was eliminated and a Python interface was
introduced. During this time, Hail was not versioned. Users had to build and use Hail directly from
the source code repository.

In March of 2017, Hail team began work on a compiler.

In October of 2018, the Hail Python interface was modified, in a backwards-incompatible way. This
new Python interface was named "Hail 0.2". The old Python interface was retroactively named "Hail
0.1". Hail team began deploying a Python package named `hail` to [PyPI](https://pypi.org). The Hail
Python package version complies with [Semantic Versioning](https://semver.org).

Meanwhile, in September of 2018, Hail team began work on a system called "Batch". Batch runs
programs in parallel on a cluster of virtual machines. Also in September, Hail team began work on a
system called "CI" (Continuous Integration). CI runs the tests for every pull request (PR) into the
`main` branch of [`hail-is/hail`](https://github.com/hail-is/hail). CI automatically merges into
main any pull request that both passes the tests and has at least one "approving" review and no
"changes requested" reviews. CI uses Hail Batch to run the tests.

Around this time, the Hail team organized itself into two sub-teams: "compilers" team and "services"
team. The compilers team is responsible for the Hail Python library, the compiler, and the
associated runtime. The compilers team code is entirely contained in the `hail` directory of the
`hail-is/hail` repository. The services team is responsible for Batch, CI, and the software
infrastructure that supports those services. Each service has its own directory in the hail
repository. More information about the structure of the repository can be found in
[`hail-overview.md`](hail-overview.md).

Beginning in early 2020, beta users were given access to Hail Batch.

In April of 2020, the Hail team began referring to the Hail Python library as "Hail Query". "Hail
Query-on-Batch" refers to the Hail Query Python library using Hail Batch to run an analysis across
many computer cores instead of using Apache Spark.

## Hail Products, Briefly

Hail team maintains four core produces.

### Hail FS

Hail FS is an filesystem-like interface which supports both local file systems and three of the
major cloud object stores: S3, ABS, and GCS. Upon this foundation, we have built tools for robustly
copying terabytes of data from one cloud to another.

These APIs are also used pervasively by Hail Batch and Hail Query-on-Batch to read and write data to
the cloud object stores.

### Hail Variant Dataset

The Hail Variant Dataset (VDS) and the [Scalable Variant Call Format
(SVCR)](https://www.biorxiv.org/content/10.1101/2024.01.09.574205v1) are a format and general
representation for sparsely storing a collection of genome sequences aligned to a reference. The VDS
is mergeable and losslessly preserves the information of a GVCF. The VDS Combiner is a robust,
horizontally-scalable-in-variants, and tree-scalable-in-samples tool for creating a VDS from one or
more GVCFs or VDSes. The VDS Combiner has been used with as many as 955,000 exomes, VDS is a
realization of the SVCR in terms of two Hail matrix tables. SVCR comprises two key sparse
representations. First, allele-indexed arrays (such as the depth per allele, AD) elide entries for
unobserved alleles. Second, homozygous reference calls are run-length encoded.



## Infrastructure and Technology

The Hail team does not maintain any physical computer infrastructure.

We maintain some virtual infrastructure, almost exclusively within the Google Cloud Platform (GCP). These include:
- a [Kubernetes](https://kubernetes.io) (k8s) cluster called `vdc` (Virtual Data Center)
- many Google Cloud Storage ([an object store](https://en.wikipedia.org/wiki/Object_storage)) buckets
- one Cloud SQL instance with a production database, ephemeral pull-request-test databases, and a
  database per developer
- logs for basically anything can be found in [GCP Logs](https://console.cloud.google.com/logs)

We use a number of technologies:
- Python is the language of choice for web applications, services, and anything user-facing
- Scala is the legacy language of the Hail Query compiler and run-time
- the JVM is the legacy target of the Hail Query compiler
- C++ is the aspirational language of high-performance services and the Hail Query compiler and run-time
- LLVM is the aspirational target of the Hail Query compiler
- Docker is our container image and run-time system
- MySQL is our SQL database of choice

### Services Technology

We almost exclusively write services in Python 3.9. We use a number of Python packages:
- [`asyncio`](https://docs.python.org/3.9/library/asyncio.html) for concurrency which is built on
  [coroutines](https://en.wikipedia.org/wiki/Coroutine) not threads
- [`aiohttp`](https://docs.aiohttp.org/en/stable/) for serving HTTPS requests (most services speak
  HTTPS)
- [`jinja2`](https://jinja.palletsprojects.com/en/2.11.x/) for "templating" which simply means
  programmatically generating text files

A service is realized as:

- a Docker image containing the executable code for the service
- a Kubernetes deployment (which defines the command to run, how much CPU is needed, what
  environment variables are set, etc.)
- a Kubernetes service (which defines which ports are accessible)
- possibly a database within our Cloud SQL instance

We call a complete, working set of all services and databases a "namespace". Every namespace
corresponds to a Kubernetes namespace. All namespaces share one CloudSQL instance, but only have
access to their databases.

The default namespace contains our "production" services and is accessible to the outside world at
https://hail.is, https://batch.hail.is, etc.

Every developer and every pull request test job also has a namespace. Developer namespaces are
accessible at https://internal.hail.is/DEVELOPER_USERNAME/SERVICE/ . Unlike the default namespace,
every other namespace has exactly one database (containing all tables from each service's database).

All incoming traffic passes through either gateway or internal-gateway which route requests to
the appropriate namespace and service. Traffic from the Internet enters the cluster through gateway,
while traffic from Batch workers enters through internal-gateway.


[^1]: https://www.who.int/genomics/geneticsVSgenomics/en/



