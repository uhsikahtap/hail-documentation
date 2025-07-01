# Table of Contents

- [Introduction to Hail](#introduction-to-hail)
  - [Background](#background)
  - [Hail Query](#hail-query)
  - [Hail Batch](#hail-batch)
  - [Source Code Organization](#source-code-organization)
  - [Hail Query Java/Scala Code Organization](#hail-query-javascala-code-organization)
  - [Microservice Overview](#microservice-overview)
  - [List of Microservices](#list-of-microservices)
  - [Hail Overview](#hail-overview)
  - [Hail for New Engineers](#hail-for-new-engineers)
  - [Genetics, Briefly](#genetics-briefly)
  - [A Brief History](#a-brief-history)
  - [Hail Products, Briefly](#hail-products-briefly)
    - [Hail FS](#hail-fs)
    - [Hail Variant Dataset](#hail-variant-dataset)
  - [Infrastructure and Technology](#infrastructure-and-technology)
    - [Services Technology](#services-technology)
- [Common Pitfalls and Essential Framework](#common-pitfalls-and-essential-framework)
  - [Core Concepts](#core-concepts)
  - [Critical Mental Model Shifts](#critical-mental-model-shifts)
    - [1. Expression System & Lazy Evaluation](#1-expression-system-lazy-evaluation)
    - [2. Data Types & Immutability](#2-data-types-immutability)
    - [3. Distributed Computation](#3-distributed-computation)
  - [Common Mistakes to Avoid](#common-mistakes-to-avoid)
    - [Type Errors & Array Operations](#type-errors-array-operations)
    - [Immutability & Annotations](#immutability-annotations)
    - [API Function Substitutions](#api-function-substitutions)
    - [Dynamic & Generalizable Code](#dynamic-generalizable-code)
    - [Efficient Data Access & Manipulation](#efficient-data-access-manipulation)
    - [Complex Structures & Nested Data](#complex-structures-nested-data)
    - [Adding Sequence to a Reference Genome](#adding-sequence-to-a-reference-genome)
  - [Debugging & Best Practices](#debugging-best-practices)
  - [Common Patterns & Examples](#common-patterns-examples)
    - [Creating Tables](#creating-tables)
- [From list of dictionaries](#from-list-of-dictionaries)
- [With intervals](#with-intervals)
    - [Efficient Lookup Creation](#efficient-lookup-creation)
- [Create table from list](#create-table-from-list)
- [Annotate with distributed computation](#annotate-with-distributed-computation)
- [Collect as lookup dictionary](#collect-as-lookup-dictionary)
- [Use in annotations](#use-in-annotations)
    - [Select and Annotate Combined](#select-and-annotate-combined)
    - [Dynamic Field Creation](#dynamic-field-creation)
- [Using ** expansion for dynamic fields](#using--expansion-for-dynamic-fields)
    - [Complex Nested Array Annotations](#complex-nested-array-annotations)
  - [Genomic-Specific Notes](#genomic-specific-notes)
- [How-To: Aggregation](#how-to-aggregation)
  - [Table Aggregations](#table-aggregations)
    - [Aggregate Over Rows Into A Local Value](#aggregate-over-rows-into-a-local-value)
      - [One aggregation](#one-aggregation)
      - [Multiple aggregations](#multiple-aggregations)
    - [Aggregate Per Group](#aggregate-per-group)
  - [Matrix Table Aggregations](#matrix-table-aggregations)
    - [Aggregate Entries Per Row (Over Columns)](#aggregate-entries-per-row-over-columns)
    - [Aggregate Entries Per Column (Over Rows)](#aggregate-entries-per-column-over-rows)
    - [Aggregate Column Values Into a Local Value](#aggregate-column-values-into-a-local-value)
      - [One aggregation](#one-aggregation-1)
      - [Multiple aggregations](#multiple-aggregations-1)
    - [Aggregate Row Values Into a Local Value](#aggregate-row-values-into-a-local-value)
      - [One aggregation](#one-aggregation-2)
      - [Multiple aggregations](#multiple-aggregations-2)
    - [Aggregate Entry Values Into A Local Value](#aggregate-entry-values-into-a-local-value)
    - [Aggregate Per Column Group](#aggregate-per-column-group)
    - [Aggregate Per Row Group](#aggregate-per-row-group)
- [How-To: Annotation](#how-to-annotation)
  - [Create a nested annotation](#create-a-nested-annotation)
  - [Remove a nested annotation](#remove-a-nested-annotation)
- [How-To: Genetics](#how-to-genetics)
  - [Formatting](#formatting)
    - [Convert variants in string format to separate locus and allele fields](#convert-variants-in-string-format-to-separate-locus-and-allele-fields)
    - [Liftover variants from one coordinate system to another](#liftover-variants-from-one-coordinate-system-to-another)
  - [Filtering and Pruning](#filtering-and-pruning)
    - [Remove related individuals from a dataset](#remove-related-individuals-from-a-dataset)
    - [Filter loci by a list of locus intervals](#filter-loci-by-a-list-of-locus-intervals)
      - [From a table of intervals](#from-a-table-of-intervals)
      - [From a UCSC BED file](#from-a-ucsc-bed-file)
      - [Using hl.filter_intervals](#using-hlfilter_intervals)
      - [Declaring intervals with hl.parse_locus_interval](#declaring-intervals-with-hlparse_locus_interval)
    - [Pruning Variants in Linkage Disequilibrium](#pruning-variants-in-linkage-disequilibrium)
  - [Analysis](#analysis)
    - [Linear Regression](#linear-regression)
      - [Single Phenotype](#single-phenotype)
      - [Multiple Phenotypes](#multiple-phenotypes)
      - [Using Variants (SNPs) as Covariates](#using-variants-snps-as-covariates)
      - [Stratified by Group](#stratified-by-group)
    - [PLINK Conversions](#plink-conversions)
      - [Polygenic Score Calculation](#polygenic-score-calculation)
  - [Create a nested annotation](#create-a-nested-annotation-1)
  - [Remove a nested annotation](#remove-a-nested-annotation-1)
- [Hail Query Python API](#hail-query-python-api)
  - [Classes](#classes)
  - [Modules](#modules)
  - [Top-Level Functions](#top-level-functions)
- [BaseTable](#basetable)
- [Table](#table)
- [GroupedTable](#groupedtable)
- [MatrixTable](#matrixtable)
- [GroupedMatrixTable](#groupedmatrixtable)
- [Expressions](#expressions)
- [Types](#types)
  - [Primitive types](#primitive-types)
  - [Container types](#container-types)
  - [Genetics types](#genetics-types)
  - [When to work with types](#when-to-work-with-types)
  - [Viewing an object's type](#viewing-an-object-s-type)
  - [Collection Types](#collection-types)
  - [Constructing types](#constructing-types)
  - [Reference documentation](#reference-documentation)
- [Functions](#functions)
- [Aggregators](#aggregators)
- [Scans](#scans)
- [Methods](#methods)
- [nd](#nd)
  - [Notes](#notes)
- [utils](#utils)
- [linalg](#linalg)
- [stats](#stats)
- [genetics](#genetics)
- [Plot](#plot)
- [Plotting With hail.ggplot Overview](#plotting-with-hailggplot-overview)
- [Variant Dataset](#variant-dataset)
  - [The data model of](#the-data-model-of)
    - [The Scalable Variant Call Representation (SVCR)](#the-scalable-variant-call-representation-svcr)
    - [Component tables](#component-tables)
    - [Building analyses on the](#building-analyses-on-the)
- [Experimental](#experimental)
  - [Contribution Guidelines](#contribution-guidelines)
  - [Annotation Database](#annotation-database)
  - [Genetics Methods](#genetics-methods)
  - [dplyr-inspired Methods](#dplyr-inspired-methods)
- [Python API](#python-api)
- [Configuration Reference](#configuration-reference)
  - [Supported Configuration Variables](#supported-configuration-variables)
- [Cheat Sheets](#cheat-sheets)
- [Datasets](#datasets)
- [Libraries](#libraries)
  - [gnomad (Hail Utilities for gnomAD)](#gnomad-hail-utilities-for-gnomad)
- [For Software Developers](#for-software-developers)
  - [Requirements](#requirements)
  - [Building Hail](#building-hail)
  - [Building the Docs and Website](#building-the-docs-and-website)
  - [Running the tests](#running-the-tests)
- [Import / Export](#import--export)
  - [Native file formats](#native-file-formats)
  - [Import](#import)
    - [General purpose](#general-purpose)
    - [Genetics](#genetics-1)
  - [Export](#export)
- [Expression](#expression)
- [BooleanExpression](#booleanexpression)
- [StructExpression](#structexpression)
- [Core language functions](#core-language-functions)
- [Constructor functions](#constructor-functions)
- [ArrayExpression](#arrayexpression)
- [ArrayNumericExpression](#arraynumericexpression)
- [CallExpression](#callexpression)
- [CollectionExpression](#collectionexpression)
- [DictExpression](#dictexpression)
- [IntervalExpression](#intervalexpression)
- [LocusExpression](#locusexpression)
- [NumericExpression](#numericexpression)
- [Int32Expression](#int32expression)
- [Int64Expression](#int64expression)
- [Float32Expression](#float32expression)
- [Float64Expression](#float64expression)
- [SetExpression](#setexpression)
- [StringExpression](#stringexpression)
- [TupleExpression](#tupleexpression)
- [NDArrayExpression](#ndarrayexpression)
- [NDArrayNumericExpression](#ndarraynumericexpression)
- [Collection functions](#collection-functions)
- [Call](#call)
- [Genetics functions](#genetics-functions)
- [Locus](#locus)
- [ReferenceGenome](#referencegenome)
- [Numeric functions](#numeric-functions)
- [Statistical functions](#statistical-functions)
- [Trio](#trio) 


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



# Common Pitfalls and Essential Framework


## Core Concepts

When working with Hail for genomics analysis, understand these fundamental principles:

1. **Distributed Data Model**: Hail is designed for large-scale computational genomics with a distributed architecture
2. **Lazy Evaluation**: Operations are deferred until explicitly triggered (`show()`, `write()`)
3. **Column-wise Operations**: Unlike pandas, Hail Tables operate column-wise, not row-wise

## Critical Mental Model Shifts

### 1. Expression System & Lazy Evaluation

- Use Hail expression system (not Python native operations)
- Operations are deferred until explicitly triggered
- Always use Hail API functions (`hl.if_else`, `hl.sum`, etc.) instead of Python equivalents
- Avoid Python loops, row-wise operations, or pandas-like methods

### 2. Data Types & Immutability

- Hail data types (`struct`, `array`, `set`) are **immutable**
- Transform data with `map` or `annotate`
- For nested structs, use `annotate` with `map` for collections
- Empty containers must use Hail constructors: `hl.empty_array()`, `hl.empty_dict()`, `hl.empty_set()`

### 3. Distributed Computation

- Always design for scalability with large datasets
- Use Hail's aggregation functions (`hl.agg.*`) for distributed computation
- Create lookup dictionaries properly with Hail tables (not driver-side loops)

## Common Mistakes to Avoid

### Type Errors & Array Operations

- **CRITICAL**: Be careful with nested type specifications for arrays and collections
- When filtering based on array comparison: `ht.filter(ht.order != hl.missing(hl.tarray(hl.tstr)))`
- NOT: `ht.filter(ht.order != hl.array(hl.missing(hl.tstr)))`
- Type errors are often cryptic; pay attention to error messages mentioning "cannot coerce type"

### Immutability & Annotations

- **CRITICAL**: Each `annotate` creates a new table - assignments must capture results
- No method chaining - each transformation step requires its own variable assignment
- Remember that filtering creates a new table that must be assigned to a variable

### API Function Substitutions

- Use `hl.if_else` not Python `if` statements
- Use `hl.missing` not `hl.null` (with proper type specification)
    - Correct: `hl.missing(hl.tarray(hl.tstr))` or `hl.missing('array<str>')`
    - **Important**: When creating empty arrays, you must specify correct nested types
    - Error example: `hl.array(hl.missing(hl.tstr))` will fail as it tries to coerce a string type to an array
- Use `|` for boolean OR operations
- For string comparisons, Hail's `<=`, `<`, `>=`, `>` operators support lexicographical comparison

### Dynamic & Generalizable Code

- Avoid hardcoding values (column names, group identifiers)
- Use `table.row`, `keys()` for dynamic field collection
- Use `hl.set` or similar for dynamic group extraction
- Use dictionary/struct expansion with `` and `*` for dynamic field creation

### Efficient Data Access & Manipulation

- Use bracket notation for dynamic field access: `x[f'{dynamic_variable}_data']`
- For dictionaries, get keys with `weights.keys().collect()[0]`
- Use `.distinct()` to subset rows so each key appears once
- Use `hl.set()` to find unique values in a list

### Complex Structures & Nested Data

- For deeply nested transformations, ensure operations remain lazy
- Remember the pattern for nested annotations:
    
    ```python
    all_positions = all_positions.annotate(
        species = all_positions.species.annotate(
            transcript_mappings = all_positions.species.transcript_mappings.filter(
                lambda x: x.region_type == 'CDS'
            )
        )
    )
    ```
    

### Adding Sequence to a Reference Genome

- Any import or read_table will overwrite the loaded sequences, so it’s best to perform these operations after any import/reading commands

```
human_rg = hl.ReferenceGenome.from_fasta_file(
    name="Homo_sapiens",
    fasta_file=args.human_fasta_path,
    index_file=args.human_index_path
)
```

## Debugging & Best Practices

1. Debug by isolating parts of computation with `show()` on intermediate steps
2. When importing files, handle unexpected BOM characters:
    
    ```python
    rename_dict = {f: f.replace('\\ufeff', '') for f in list(ht.row.dtype)}
    
    ```
    
3. For aggregations in grouped tables, use `hl.agg.collect_as_set()` or `hl.agg.take(ht.Field, 1)[0]`
4. With VCF imports, use `skip_invalid_loci=True` and `array_elements_required=False`
5. Remember reference genome contig naming conventions:
    - hg38: contigs have 'chr' prefix
    - hg37: contigs do not have 'chr' prefix

## Common Patterns & Examples

### Creating Tables

```python
# From list of dictionaries
t = hl.Table.parallelize(
    [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    schema=hl.tstruct(a=hl.tint, b=hl.tint)
)

# With intervals
ht = hl.Table.parallelize(
    [{'interval': interval} for interval in intervals],
    schema=hl.tstruct(interval=hl.tinterval(hl.tlocus("GRCh38")))
)

```

### Efficient Lookup Creation

```python
# Create table from list
sample_info_table = hl.Table.parallelize(
    [{'Child_ID': x} for x in child_ids],
    schema=hl.tstruct(Child_ID=hl.tstr)
)

# Annotate with distributed computation
sample_info_table = sample_info_table.annotate(
    sample_info = hl.struct(
        child = sample_info_table.Child_ID,
        some_metric = hl.len(sample_info_table.Child_ID)
    )
)

# Collect as lookup dictionary
sample_info_lookup = hl.dict({
    row.Child_ID: row.sample_info
    for row in sample_info_table.collect()
})

# Use in annotations
dummy_table = dummy_table.annotate(
    sample_info = sample_info_lookup[dummy_table.child_id]
)

#NOTE: using the .get parameter of hail dicts can be computationally expensive. It's best to use the bracket lookup which will throw an error if the key is not found.
```

### Select and Annotate Combined

```python
syn_sites = syn_sites.select(
    locus = hl.locus(
        contig=hl.str("chr" + syn_sites.CHROM),
        position=hl.int(syn_sites.POS),
        reference_genome='GRCh38'
    ),
    alleles = [syn_sites.REF, syn_sites.ALT],
    roulette_syn_scaling_site = True
)

```

### Dynamic Field Creation

```python
# Using ** expansion for dynamic fields
hl.struct(
    annotation=group,
    **{
        f"{f}_def_species": hl.sum(
            all_positions.combined
                .filter(lambda x: x.annotation == group)
                .map(lambda x: x[f])
        ) for f in fields
    }
)

#NOTE: Be careful when annotating with many rows, sometimes including a checkpoint between annotations can speed up the computation as the hail backend may get too confused

```

### Complex Nested Array Annotations

```python
trost_data = trost_data.annotate(
    trost_data = trost_data.trost_data.annotate(
        trost_sample_info = hl.map(
            lambda x: hl.struct(
                **{k: x[k] for k in fields},
                flags = hl.if_else(
                    x.gene_counts_over2.any(lambda y: hl.int(y[0]) > 2),
                    x.flags.extend(['gene_over2']),
                    x.flags
                )
            ),
            trost_data.trost_data.trost_sample_info
        )
    )
)

```

## Genomic-Specific Notes

- Allowed contigs: `hl.set([str(x) for x in range(1, 23)] + ["X", "Y", "M"])`
- Locus intervals: `hl.locus_interval("1", 100, 1000, reference_genome='GRCh37')`
- When working with genetic data, remember the difference between locus and variant:
    - A locus has one reference allele (e.g., A)
    - A locus can have multiple alternate alleles (T, C, G)
    - Data may have multiple variant rows at the same locus

Other Notes

-

`hl.flatmap(**lambda** x: x[1:], a)`

Error: invalid decimal literal tp_data.gnomAD4.1_joint_max_AF —> tp_data['gnomAD4.1_joint_max_AF']

Only one of these command per line: key_by, select, annotate

No ‘.’ in any column name 

Best practices:

```
    clean_clinvar_domNDD = hl.if_else(
        hl.is_missing(primate_data.clean_clinvar_domNDD),
        False,
        primate_data.clean_clinvar_domNDD
    )
    
    #hl.if_else(
	  #  primate_data.clean_clinvar_domNDD == True,
	  #  True,
	  #  False
	  #)
	  #This will result in None for any missing value not false
```

- Handle missing cases first
    - hl.if_else(primate_data.clean_clinvar_domNDD == True)


# How-To: Aggregation
For a full list of aggregators, see the `aggregators` section of the API reference.

## Table Aggregations

### Aggregate Over Rows Into A Local Value

#### One aggregation
**Description:**
Compute the fraction of rows where `SEX` == `'M'` in a table.

**Code:**
```python
>>> ht.aggregate(hl.agg.fraction(ht.SEX == 'M'))
0.5
```

**Dependencies:** `Table.aggregate()`, `aggregators.fraction()`

#### Multiple aggregations
**Description:**
Compute two aggregation statistics, the fraction of rows where `SEX` == `'M'` and the mean value of `X`, from the rows of a table.

**Code:**
```python
>>> ht.aggregate(hl.struct(fraction_male = hl.agg.fraction(ht.SEX == 'M'),
...                        mean_x = hl.agg.mean(ht.X)))
Struct(fraction_male=0.5, mean_x=6.5)
```

**Dependencies:** `Table.aggregate()`, `aggregators.fraction()`, `aggregators.mean()`, `StructExpression`

### Aggregate Per Group
**Description:**
Group the table `ht` by `ID` and compute the mean value of `X` per group.

**Code:**
```python
>>> result_ht = ht.group_by(ht.ID).aggregate(mean_x=hl.agg.mean(ht.X))
```

**Dependencies:** `Table.group_by()`, `GroupedTable.aggregate()`, `aggregators.mean()`

## Matrix Table Aggregations

### Aggregate Entries Per Row (Over Columns)
**Description:**
Count the number of occurrences of each unique `GT` field per row, i.e. aggregate over the columns of the matrix table.
Methods `MatrixTable.filter_rows()`, `MatrixTable.select_rows()`, and `MatrixTable.transmute_rows()` also support aggregation over columns.

**Code:**
```python
>>> result_mt = mt.annotate_rows(gt_counter=hl.agg.counter(mt.GT))
```

**Dependencies:** `MatrixTable.annotate_rows()`, `aggregators.counter()`

### Aggregate Entries Per Column (Over Rows)
**Description:**
Compute the mean of the `GQ` field per column, i.e. aggregate over the rows of the MatrixTable.
Methods `MatrixTable.filter_cols()`, `MatrixTable.select_cols()`, and `MatrixTable.transmute_cols()` also support aggregation over rows.

**Code:**
```python
>>> result_mt = mt.annotate_cols(gq_mean=hl.agg.mean(mt.GQ))
```

**Dependencies:** `MatrixTable.annotate_cols()`, `aggregators.mean()`

### Aggregate Column Values Into a Local Value

#### One aggregation
**Description:**
Aggregate over the column-indexed field `pheno.is_female` to compute the fraction of female samples in the matrix table.

**Code:**
```python
>>> mt.aggregate_cols(hl.agg.fraction(mt.pheno.is_female))
0.44
```

**Dependencies:** `MatrixTable.aggregate_cols()`, `aggregators.fraction()`

#### Multiple aggregations
**Description:**
Perform multiple aggregations over column-indexed fields by using a struct expression. The result is a single struct containing two nested fields, `fraction_female` and `case_ratio`.

**Code:**
```python
>>> mt.aggregate_cols(hl.struct(
...         fraction_female=hl.agg.fraction(mt.pheno.is_female),
...         case_ratio=hl.agg.count_where(mt.is_case) / hl.agg.count()))
Struct(fraction_female=0.44, case_ratio=1.0)
```

**Dependencies:** `MatrixTable.aggregate_cols()`, `aggregators.fraction()`, `aggregators.count_where()`, `StructExpression`

### Aggregate Row Values Into a Local Value

#### One aggregation
**Description:**
Compute the mean value of the row-indexed field `qual`.

**Code:**
```python
>>> mt.aggregate_rows(hl.agg.mean(mt.qual))
140054.73333333334
```

**Dependencies:** `MatrixTable.aggregate_rows()`, `aggregators.mean()`

#### Multiple aggregations
**Description:**
Perform two row aggregations: count the number of row values of `qual` that are greater than 40, and compute the mean value of `qual`. The result is a single struct containing two nested fields, `n_high_quality` and `mean_qual`.

**Code:**
```python
>>> mt.aggregate_rows(
...             hl.struct(n_high_quality=hl.agg.count_where(mt.qual > 40),
...                       mean_qual=hl.agg.mean(mt.qual)))
Struct(n_high_quality=9, mean_qual=140054.73333333334)
```

**Dependencies:** `MatrixTable.aggregate_rows()`, `aggregators.count_where()`, `aggregators.mean()`, `StructExpression`

### Aggregate Entry Values Into A Local Value
**Description:**
Compute the mean of the entry-indexed field `GQ` and the call rate of the entry-indexed field `GT`. The result is returned as a single struct with two nested fields.

**Code:**
```python
>>> mt.aggregate_entries(
...     hl.struct(global_gq_mean=hl.agg.mean(mt.GQ),
...               call_rate=hl.agg.fraction(hl.is_defined(mt.GT))))
Struct(global_gq_mean=69.60514541387025, call_rate=0.9933333333333333)
```

**Dependencies:** `MatrixTable.aggregate_entries()`, `aggregators.mean()`, `aggregators.fraction()`, `StructExpression`

### Aggregate Per Column Group
**Description:**
Group the columns of the matrix table by the column-indexed field `cohort` and compute the call rate per cohort.

**Code:**
```python
>>> result_mt = (mt.group_cols_by(mt.cohort)
...              .aggregate(call_rate=hl.agg.fraction(hl.is_defined(mt.GT))))
```

**Dependencies:** `MatrixTable.group_cols_by()`, `GroupedMatrixTable`, `GroupedMatrixTable.aggregate()`

<details>
<summary>Understanding</summary>

Group the columns of the matrix table by the column-indexed field `cohort` using `MatrixTable.group_cols_by()`, which returns a `GroupedMatrixTable`. Then use `GroupedMatrixTable.aggregate()` to compute an aggregation per column group.
The result is a matrix table with an entry field `call_rate` that contains the result of the aggregation. The new matrix table has a row schema equal to the original row schema, a column schema equal to the fields passed to `group_cols_by`, and an entry schema determined by the expression passed to `aggregate`. Other column fields and entry fields are dropped.

</details>

### Aggregate Per Row Group
**Description:**
Compute the number of calls with one or more non-reference alleles per gene group.

**Code:**
```python
>>> result_mt = (mt.group_rows_by(mt.gene)
...              .aggregate(n_non_ref=hl.agg.count_where(mt.GT.is_non_ref())))
```

**Dependencies:** `MatrixTable.group_rows_by()`, `GroupedMatrixTable`, `GroupedMatrixTable.aggregate()`

<details>
<summary>Understanding</summary>

Group the rows of the matrix table by the row-indexed field `gene` using `MatrixTable.group_rows_by()`, which returns a `GroupedMatrixTable`. Then use `GroupedMatrixTable.aggregate()` to compute an aggregation per grouped row.
The result is a matrix table with an entry field `n_non_ref` that contains the result of the aggregation. This new matrix table has a row schema equal to the fields passed to `group_rows_by`, a column schema equal to the column schema of the original matrix table, and an entry schema determined by the expression passed to `aggregate`. Other row fields and entry fields are dropped.

</details>



# How-To: Annotation

Annotations are Hail’s way of adding data fields to Hail’s tables and matrix tables.

## Create a nested annotation

**Description:**
Add a new field `gq_mean` as a nested field inside `info`.

**Code:**
```python
>>> mt = mt.annotate_rows(info=mt.info.annotate(gq_mean=hl.agg.mean(mt.GQ)))
```

**Dependencies:** `StructExpression.annotate()`, `MatrixTable.annotate_rows()`

<details>
<summary>Understanding</summary>

To add a new field `gq_mean` as a nested field inside `info`, instead of a top-level field, we need to annotate the `info` field itself.

Construct an expression `mt.info.annotate(gq_mean=...)` which adds the field to `info`. Then, reassign this expression to `info` using `MatrixTable.annotate_rows()`.

</details>

## Remove a nested annotation

**Description:**
Drop a field `AF`, which is nested inside the `info` field.

**Code:**
```python
>>> mt = mt.annotate_rows(info=mt.info.drop('AF'))
```

**Dependencies:** `StructExpression.drop()`, `MatrixTable.annotate_rows()`

<details>
<summary>Understanding</summary>

To drop a nested field `AF`, construct an expression `mt.info.drop('AF')` which drops the field from its parent field, `info`. Then, reassign this expression to `info` using `MatrixTable.annotate_rows()`.

</details>

# How-To: Genetics
This page tailored how-to guides for small but commonly-used patterns appearing in genetics pipelines. For documentation on the suite of genetics functions built into Hail, see the genetics methods page.

## Formatting

### Convert variants in string format to separate locus and allele fields

**Code:**
```python
>>> ht = ht.key_by(**hl.parse_variant(ht.variant))
```
**Dependencies:** `parse_variant()`, `key_by()`

<details>
<summary>Understanding</summary>

If your variants are strings of the format ‘chr:pos:ref:alt’, you may want to convert them to separate locus and allele fields. This is useful if you have imported a table with variants in string format and you would like to join this table with other Hail tables that are keyed by locus and alleles.

`hl.parse_variant(ht.variant)` constructs a StructExpression containing two nested fields for the locus and alleles. The `**` syntax unpacks this struct so that the resulting table has two new fields, `locus` and `alleles`.

</details>

### Liftover variants from one coordinate system to another
**Tags:** `liftover`

**Description:**
Liftover a Table or MatrixTable from one reference genome to another.

**Code:**
First, we need to set up the two reference genomes (source and destination):
```python
>>> rg37 = hl.get_reference('GRCh37')  
>>> rg38 = hl.get_reference('GRCh38')  
>>> rg37.add_liftover('gs://hail-common/references/grch37_to_grch38.over.chain.gz', rg38)
```
Then we can liftover the locus coordinates in a Table or MatrixTable (here, `ht`) from reference genome 'GRCh37' to 'GRCh38':
```python
>>> ht = ht.annotate(new_locus=hl.liftover(ht.locus, 'GRCh38'))  
>>> ht = ht.filter(hl.is_defined(ht.new_locus))  
>>> ht = ht.key_by(locus=ht.new_locus)
```  
Note that this approach does not retain the old locus, nor does it verify that the allele has not changed strand. We can keep the old one for reference and filter out any liftover that changed strands using:
```python
>>> ht = ht.annotate(new_locus=hl.liftover(ht.locus, 'GRCh38', include_strand=True),
...                  old_locus=ht.locus)  
>>> ht = ht.filter(hl.is_defined(ht.new_locus) & ~ht.new_locus.is_negative_strand)  
>>> ht = ht.key_by(locus=ht.new_locus.result)
```  
**Dependencies:** `liftover()`, `add_liftover()`, `get_reference()`

## Filtering and Pruning

### Remove related individuals from a dataset
**Tags:** `kinship`

**Description:**
Compute a measure of kinship between individuals, and then prune related individuals from a matrix table.

**Code:**
```python
>>> pc_rel = hl.pc_relate(mt.GT, 0.001, k=2, statistics='kin')
>>> pairs = pc_rel.filter(pc_rel['kin'] > 0.125)
>>> related_samples_to_remove = hl.maximal_independent_set(pairs.i, pairs.j,
...                                                        keep=False)
>>> result = mt.filter_cols(
...     hl.is_defined(related_samples_to_remove[mt.col_key]), keep=False)
```
**Dependencies:** `pc_relate()`, `maximal_independent_set()`

<details>
<summary>Understanding</summary>

To remove related individuals from a dataset, we first compute a measure of relatedness between individuals using `pc_relate()`. We filter this result based on a kinship threshold, which gives us a table of related pairs.

From this table of pairs, we can compute the complement of the maximal independent set using `maximal_independent_set()`. The parameter `keep=False` in `maximal_independent_set` specifies that we want the complement of the set (the variants to remove), rather than the maximal independent set itself. It’s important to use the complement for filtering, rather than the set itself, because the maximal independent set will not contain the singleton individuals.

Once we have a list of samples to remove, we can filter the columns of the dataset to remove the related individuals.
</details>

### Filter loci by a list of locus intervals

#### From a table of intervals
**Tags:** `genomic region`, `genomic range`

**Description:**
Import a text file of locus intervals as a table, then use this table to filter the loci in a matrix table.

**Code:**
```python
>>> interval_table = hl.import_locus_intervals('data/gene.interval_list', reference_genome='GRCh37')
>>> filtered_mt = mt.filter_rows(hl.is_defined(interval_table[mt.locus]))
```
**Dependencies:** `import_locus_intervals()`, `MatrixTable.filter_rows()`

<details>
<summary>Understanding</summary>

We have a matrix table `mt` containing the loci we would like to filter, and a list of locus intervals stored in a file. We can import the intervals into a table with `import_locus_intervals()`.

Hail supports implicit joins between locus intervals and loci, so we can filter our dataset to the rows defined in the join between the interval table and our matrix table.

`interval_table[mt.locus]` joins the matrix table with the table of intervals based on locus and `interval<locus>` matches. This is a `StructExpression`, which will be defined if the locus was found in any interval, or missing if the locus is outside all intervals.

To do our filtering, we can filter to the rows of our matrix table where the struct expression `interval_table[mt.locus]` is defined.

This method will also work to filter a table of loci, as well as a matrix table.
</details>

#### From a UCSC BED file
**Description:**
Import a UCSC BED file as a table of intervals, then use this table to filter the loci in a matrix table.

**Code:**
```python
>>> interval_table = hl.import_bed('data/file1.bed', reference_genome='GRCh37')
>>> filtered_mt = mt.filter_rows(hl.is_defined(interval_table[mt.locus]))
```
**Dependencies:** `import_bed()`, `MatrixTable.filter_rows()`

#### Using hl.filter_intervals
**Description:**
Filter using an interval table, suitable for a small list of intervals.

**Code:**
```python
>>> filtered_mt = hl.filter_intervals(mt, interval_table['interval'].collect())
```
**Dependencies:** `methods.filter_intervals()`

#### Declaring intervals with hl.parse_locus_interval
**Description:**
Filter to declared intervals.

**Code:**
```python
>>> intervals = ['1:100M-200M', '16:29.1M-30.2M', 'X']
>>> filtered_mt = hl.filter_intervals(
...     mt,
...     [hl.parse_locus_interval(x, reference_genome='GRCh37') for x in intervals])
```
**Dependencies:** `methods.filter_intervals()`, `parse_locus_interval()`

### Pruning Variants in Linkage Disequilibrium
**Tags:** `LD Prune`

**Description:**
Remove correlated variants from a matrix table.

**Code:**
```python
>>> biallelic_mt = mt.filter_rows(hl.len(mt.alleles) == 2)
>>> pruned_variant_table = hl.ld_prune(mt.GT, r2=0.2, bp_window_size=500000)
>>> filtered_mt = mt.filter_rows(
...     hl.is_defined(pruned_variant_table[mt.row_key]))
```
**Dependencies:** `ld_prune()`

<details>
<summary>Understanding</summary>

Hail’s `ld_prune()` method takes a matrix table and returns a table with a subset of variants which are uncorrelated with each other. The method requires a biallelic dataset, so we first filter our dataset to biallelic variants. Next, we get a table of independent variants using `ld_prune()`, which we can use to filter the rows of our original dataset.

Note that it is more efficient to do the final filtering step on the original dataset, rather than on the biallelic dataset, so that the biallelic dataset does not need to be recomputed.
</details>

## Analysis

### Linear Regression

#### Single Phenotype
**Tags:** `Linear Regression`

**Description:**
Compute linear regression statistics for a single phenotype.

**Code:**
Approach #1: Use the `linear_regression_rows()` method
```python
>>> ht = hl.linear_regression_rows(y=mt.pheno.height,
...                                x=mt.GT.n_alt_alleles(),
...                                covariates=[1])
```
Approach #2: Use the `aggregators.linreg()` aggregator
```python
>>> mt_linreg = mt.annotate_rows(linreg=hl.agg.linreg(y=mt.pheno.height,
...                                                   x=[1, mt.GT.n_alt_alleles()]))
```
**Dependencies:** `linear_regression_rows()`, `aggregators.linreg()`

<details>
<summary>Understanding</summary>

The `linear_regression_rows()` method is more efficient than using the `aggregators.linreg()` aggregator. However, the `aggregators.linreg()` aggregator is more flexible (multiple covariates can vary by entry) and returns a richer set of statistics.
</details>

#### Multiple Phenotypes
**Tags:** `Linear Regression`

**Description:**
Compute linear regression statistics for multiple phenotypes.

**Code:**
Approach #1: Use the `linear_regression_rows()` method for all phenotypes simultaneously
```python
>>> ht_result = hl.linear_regression_rows(y=[mt.pheno.height, mt.pheno.blood_pressure],
...                                       x=mt.GT.n_alt_alleles(),
...                                       covariates=[1])
```
Approach #2: Use the `linear_regression_rows()` method for each phenotype sequentially
```python
>>> ht1 = hl.linear_regression_rows(y=mt.pheno.height,
...                                 x=mt.GT.n_alt_alleles(),
...                                 covariates=[1])
>>> ht2 = hl.linear_regression_rows(y=mt.pheno.blood_pressure,
...                                 x=mt.GT.n_alt_alleles(),
...                                 covariates=[1])
```
Approach #3: Use the `aggregators.linreg()` aggregator
```python
>>> mt_linreg = mt.annotate_rows(
...     linreg_height=hl.agg.linreg(y=mt.pheno.height,
...                                 x=[1, mt.GT.n_alt_alleles()]),
...     linreg_bp=hl.agg.linreg(y=mt.pheno.blood_pressure,
...                             x=[1, mt.GT.n_alt_alleles()]))
```
**Dependencies:** `linear_regression_rows()`, `aggregators.linreg()`

<details>
<summary>Understanding</summary>

The `linear_regression_rows()` method is more efficient than using the `aggregators.linreg()` aggregator, especially when analyzing many phenotypes. However, the `aggregators.linreg()` aggregator is more flexible (multiple covariates can vary by entry) and returns a richer set of statistics. The `linear_regression_rows()` method drops samples that have a missing value for any of the phenotypes. Therefore, Approach #1 may not be suitable for phenotypes with differential patterns of missingness. Approach #2 will do two passes over the data while Approaches #1 and #3 will do one pass over the data and compute the regression statistics for each phenotype simultaneously.
</details>

#### Using Variants (SNPs) as Covariates
**Tags:** `sample genotypes covariate`

**Description:**
Use sample genotype dosage at specific variant(s) as covariates in regression routines.

**Code:**
Create a sample annotation from the genotype dosage for each variant of interest by combining the filter and collect aggregators:
```python
>>> mt_annot = mt.annotate_cols(
...     snp1 = hl.agg.filter(hl.parse_variant('20:13714384:A:C') == mt.row_key,
...                          hl.agg.collect(mt.GT.n_alt_alleles()))[0],
...     snp2 = hl.agg.filter(hl.parse_variant('20:17479730:T:C') == mt.row_key,
...                          hl.agg.collect(mt.GT.n_alt_alleles()))[0])
```
Run the GWAS with `linear_regression_rows()` using variant dosages as covariates:
```python
>>> gwas = hl.linear_regression_rows(  
...     x=mt_annot.GT.n_alt_alleles(),
...     y=mt_annot.pheno.blood_pressure,
...     covariates=[1, mt_annot.pheno.age, mt_annot.snp1, mt_annot.snp2])
```
**Dependencies:** `linear_regression_rows()`, `aggregators.collect()`, `parse_variant()`, `variant_str()`

#### Stratified by Group
**Tags:** `Linear Regression`

**Description:**
Compute linear regression statistics for a single phenotype stratified by group.

**Code:**
Approach #1: Use the `linear_regression_rows()` method for each group
```python
>>> female_pheno = (hl.case()
...                   .when(mt.pheno.is_female, mt.pheno.height)
...                   .or_missing())
>>> linreg_female = hl.linear_regression_rows(y=female_pheno,
...                                           x=mt.GT.n_alt_alleles(),
...                                           covariates=[1])
>>> male_pheno = (hl.case()
...                 .when(~mt.pheno.is_female, mt.pheno.height)
...                 .or_missing())
>>> linreg_male = hl.linear_regression_rows(y=male_pheno,
...                                         x=mt.GT.n_alt_alleles(),
...                                         covariates=[1])
```
Approach #2: Use the `aggregators.group_by()` and `aggregators.linreg()` aggregators
```python
>>> mt_linreg = mt.annotate_rows(
...     linreg=hl.agg.group_by(mt.pheno.is_female,
...                            hl.agg.linreg(y=mt.pheno.height,
...                                          x=[1, mt.GT.n_alt_alleles()])))
```
**Dependencies:** `linear_regression_rows()`, `aggregators.group_by()`, `aggregators.linreg()`

<details>
<summary>Understanding</summary>

We have presented two ways to compute linear regression statistics for each value of a grouping variable. The first approach utilizes the `linear_regression_rows()` method and must be called separately for each group even though it can compute statistics for multiple phenotypes simultaneously. This is because the `linear_regression_rows()` method drops samples that have a missing value for any of the phenotypes. When the groups are mutually exclusive, such as ‘Male’ and ‘Female’, no samples remain! Note that we cannot define `male_pheno = ~female_pheno` because we subsequently need `male_pheno` to be an expression on the `mt_linreg` matrix table rather than `mt`. Lastly, the argument to `root` must be specified for both cases – otherwise the ‘Male’ output will overwrite the ‘Female’ output.

The second approach uses the `aggregators.group_by()` and `aggregators.linreg()` aggregators. The aggregation expression generates a dictionary where a key is a group (value of the grouping variable) and the corresponding value is the linear regression statistics for those samples in the group. The result of the aggregation expression is then used to annotate the matrix table.

The `linear_regression_rows()` method is more efficient than the `aggregators.linreg()` aggregator and can be extended to multiple phenotypes, but the `aggregators.linreg()` aggregator is more flexible (multiple covariates can be vary by entry) and returns a richer set of statistics.
</details>

### PLINK Conversions

#### Polygenic Score Calculation
> **plink:**
> ```
> plink --bfile data --score scores.txt sum
> ```

**Tags:** `PRS`

**Description:**
This command is analogous to plink’s `–score` command with the `sum` option. Biallelic variants are required.

**Code:**
```python
>>> mt = hl.import_plink(
...     bed="data/ldsc.bed", bim="data/ldsc.bim", fam="data/ldsc.fam",
...     quant_pheno=True, missing='-9')
>>> mt = hl.variant_qc(mt)
>>> scores = hl.import_table('data/scores.txt', delimiter=' ', key='rsid',
...                          types={'score': hl.tfloat32})
>>> mt = mt.annotate_rows(**scores[mt.rsid])
>>> flip = hl.case().when(mt.allele == mt.alleles[0], True).when(
...     mt.allele == mt.alleles[1], False).or_missing()
>>> mt = mt.annotate_rows(flip=flip)
>>> mt = mt.annotate_rows(
...     prior=2 * hl.if_else(mt.flip, mt.variant_qc.AF[0], mt.variant_qc.AF[1]))
>>> mt = mt.annotate_cols(
...     prs=hl.agg.sum(
...         mt.score * hl.coalesce(
...             hl.if_else(mt.flip, 2 - mt.GT.n_alt_alleles(),
...                        mt.GT.n_alt_alleles()), mt.prior)))
```
**Dependencies:** `import_plink()

Annotations are Hail’s way of adding data fields to Hail’s tables and matrix tables.

## Create a nested annotation

**Description:**
Add a new field `gq_mean` as a nested field inside `info`.

**Code:**
```python
>>> mt = mt.annotate_rows(info=mt.info.annotate(gq_mean=hl.agg.mean(mt.GQ)))
```

**Dependencies:** `StructExpression.annotate()`, `MatrixTable.annotate_rows()`

<details>
<summary>Understanding</summary>

To add a new field `gq_mean` as a nested field inside `info`, instead of a top-level field, we need to annotate the `info` field itself.

Construct an expression `mt.info.annotate(gq_mean=...)` which adds the field to `info`. Then, reassign this expression to `info` using `MatrixTable.annotate_rows()`.

</details>

## Remove a nested annotation

**Description:**
Drop a field `AF`, which is nested inside the `info` field.

**Code:**
```python
>>> mt = mt.annotate_rows(info=mt.info.drop('AF'))
```

**Dependencies:** `StructExpression.drop()`, `MatrixTable.annotate_rows()`

<details>
<summary>Understanding</summary>

To drop a nested field `AF`, construct an expression `mt.info.drop('AF')` which drops the field from its parent field, `info`. Then, reassign this expression to `info` using `MatrixTable.annotate_rows()`.

</details>

# Hail Query Python API

This is the API documentation for `Hail Query`, and provides detailed
information on the Python programming interface.

Use `import hail as hl` to access this functionality.

## Classes

```hail.table.BaseTable``` | Base class for ```Table```, ```GroupedTable```, ```hail.matrixtable.MatrixTable```, and ```hail.matrixtable.GroupedMatrixTable```  
---|---  
```hail.Table``` | Hail's distributed implementation of a dataframe or SQL table.  
```hail.GroupedTable``` | Table grouped by row that can be aggregated into a new table.  
```hail.MatrixTable``` | Hail's distributed implementation of a structured matrix.  
```hail.GroupedMatrixTable``` | Matrix table grouped by row or column that can be aggregated into a new matrix table.  
  
## Modules

  * ``expressions``
  * ``types``
  * ``functions``
  * ``aggregators``
  * ``scans``
  * ``methods``
  * ``nd``
  * ``utils``
  * ``linalg``
  * ``stats``
  * ``genetics``
  * ``plot``
  * ``ggplot``
  * ``vds``
  * ``experimental``

## Top-Level Functions

hail.init(_sc =None_, _app_name =None_, _master =None_, _local ='local[*]'_,
_log =None_, _quiet =False_, _append =False_, _min_block_size =0_,
_branching_factor =50_, _tmp_dir =None_, _default_reference =None_,
_idempotent =False_, _global_seed =None_, _spark_conf =None_,
_skip_logging_configuration =False_, _local_tmpdir =None_,
__optimizer_iterations =None_, _*_ , _backend =None_, _driver_cores =None_,
_driver_memory =None_, _worker_cores =None_, _worker_memory =None_, _batch_id
=None_, _gcs_requester_pays_configuration =None_, _regions =None_,
_gcs_bucket_allow_list =None_, _copy_spark_log_on_error
=False_)[[source]](_modules/hail/context.html#init)

    

Initialize and configure Hail.

This function will be called with default arguments if any Hail functionality
is used. If you need custom configuration, you must explicitly call this
function before using Hail. For example, to set the global random seed to 0,
import Hail and immediately call `init()`:

    
    
    >>> import hail as hl
    >>> hl.init(global_seed=0)  
    

Hail has two backends, `spark` and `batch`. Hail selects a backend by
consulting, in order, these configuration locations:

  1. The `backend` parameter of this function.

  2. The `HAIL_QUERY_BACKEND` environment variable.

  3. The value of `hailctl config get query/backend`.

If no configuration is found, Hail will select the Spark backend.

Examples

Configure Hail to use the Batch backend:

    
    
    >>> import hail as hl
    >>> hl.init(backend='batch')  
    

If a
[`pyspark.SparkContext`](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.SparkContext.html#pyspark.SparkContext
"\(in PySpark vmaster\)") is already running, then Hail must be initialized
with it as an argument:

    
    
    >>> hl.init(sc=sc)  
    

Configure Hail to bill to my_project when accessing any Google Cloud Storage
bucket that has requester pays enabled:

    
    
    >>> hl.init(gcs_requester_pays_configuration='my-project')  
    

Configure Hail to bill to my_project when accessing the Google Cloud Storage
buckets named bucket_of_fish and bucket_of_eels:

    
    
    >>> hl.init(
    ...     gcs_requester_pays_configuration=('my-project', ['bucket_of_fish', 'bucket_of_eels'])
    ... )  
    

You may also use hailctl config set gcs_requester_pays/project and hailctl
config set gcs_requester_pays/buckets to achieve the same effect.

See also

`stop()`

Parameters:

    

  * **sc** (_pyspark.SparkContext, optional_) – Spark Backend only. Spark context. If not specified, the Spark backend will create a new Spark context.

  * **app_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – A name for this pipeline. In the Spark backend, this becomes the Spark application name. In the Batch backend, this is a prefix for the name of every Batch.

  * **master** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Spark Backend only. URL identifying the Spark leader (master) node or local[N] for local clusters.

  * **local** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Spark Backend only. Local-mode core limit indicator. Must either be local[N] where N is a positive integer or local[*]. The latter indicates Spark should use all cores available. local[*] does not respect most containerization CPU limits. This option is only used if master is unset and spark.master is not set in the Spark configuration.

  * **log** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Local path for Hail log file. Does not currently support distributed file systems like Google Storage, S3, or HDFS.

  * **quiet** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print fewer log messages.

  * **append** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Append to the end of the log file.

  * **min_block_size** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Minimum file block size in MB.

  * **branching_factor** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Branching factor for tree aggregation.

  * **tmp_dir** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Networked temporary directory. Must be a network-visible file path. Defaults to /tmp in the default scheme.

  * **default_reference** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – _Deprecated_. Please use `default_reference()` to set the default reference genome

Default reference genome. Either `'GRCh37'`, `'GRCh38'`, `'GRCm38'`, or
`'CanFam3'`.

  * **idempotent** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If `True`, calling this function is a no-op if Hail has already been initialized.

  * **global_seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Global random seed.

  * **spark_conf** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to :class`str`, optional) – Spark backend only. Spark configuration parameters.

  * **skip_logging_configuration** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Spark Backend only. Skip logging configuration in java and python.

  * **local_tmpdir** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Local temporary directory. Used on driver and executor nodes. Must use the file scheme. Defaults to TMPDIR, or /tmp.

  * **driver_cores** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Batch backend only. Number of cores to use for the driver process. May be 1, 2, 4, or 8. Default is 1.

  * **driver_memory** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Batch backend only. Memory tier to use for the driver process. May be standard or highmem. Default is standard.

  * **worker_cores** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Batch backend only. Number of cores to use for the worker processes. May be 1, 2, 4, or 8. Default is 1.

  * **worker_memory** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – Batch backend only. Memory tier to use for the worker processes. May be standard or highmem. Default is standard.

  * **batch_id** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Batch backend only. An existing batch id to add jobs to.

  * **gcs_requester_pays_configuration** (either [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") and [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – If a string is provided, configure the Google Cloud Storage file system to bill usage to the project identified by that string. If a tuple is provided, configure the Google Cloud Storage file system to bill usage to the specified project for buckets specified in the list. See examples above.

  * **regions** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – List of regions to run jobs in when using the Batch backend. Use ```ANY_REGION``` to specify any region is allowed or use None to use the underlying default regions from the hailctl environment configuration. For example, use hailctl config set batch/regions region1,region2 to set the default regions to use.

  * **gcs_bucket_allow_list** – A list of buckets that Hail should be permitted to read from or write to, even if their default policy is to use “cold” storage. Should look like `["bucket1", "bucket2"]`.

  * **copy_spark_log_on_error** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)"), optional) – Spark backend only. If True, copy the log from the spark driver node to tmp_dir on error.

hail.asc(_col_)[[source]](_modules/hail/table.html#asc)

    

Sort by col ascending.

hail.desc(_col_)[[source]](_modules/hail/table.html#desc)

    

Sort by col descending.

hail.stop()[[source]](_modules/hail/context.html#stop)

    

Stop the currently running Hail session.

hail.spark_context()[[source]](_modules/hail/context.html#spark_context)

    

Returns the active Spark context.

Returns:

    

[`pyspark.SparkContext`](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.SparkContext.html#pyspark.SparkContext
"\(in PySpark vmaster\)")

hail.tmp_dir()[[source]](_modules/hail/context.html#tmp_dir)

    

Returns the Hail shared temporary directory.

Returns:

    

[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")

hail.default_reference(_new_default_reference
=None_)[[source]](_modules/hail/context.html#default_reference)

    

With no argument, returns the default reference genome (`'GRCh37'` by
default). With an argument, sets the default reference genome to the argument.

Returns:

    

[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome")

hail.get_reference(_name_)[[source]](_modules/hail/context.html#get_reference)

    

Returns the reference genome corresponding to name.

Notes

Hail’s built-in references are `'GRCh37'`, `GRCh38'`, `'GRCm38'`, and
`'CanFam3'`. The contig names and lengths come from the GATK resource bundle:
[human_g1k_v37.dict](ftp://gsapubftp-
anonymous@ftp.broadinstitute.org/bundle/b37/human_g1k_v37.dict) and
[Homo_sapiens_assembly38.dict](ftp://gsapubftp-
anonymous@ftp.broadinstitute.org/bundle/hg38/Homo_sapiens_assembly38.dict).

If `name='default'`, the value of `default_reference()` is returned.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Name of a previously loaded reference genome or one of
Hail’s built-in references: `'GRCh37'`, `'GRCh38'`, `'GRCm38'`, `'CanFam3'`,
and `'default'`.

Returns:

    

[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome")

hail.set_global_seed(_seed_)[[source]](_modules/hail/context.html#set_global_seed)

    

Deprecated.

Has no effect. To ensure reproducible randomness, use the global_seed argument
to `init()` and `reset_global_randomness()`.

See the ``random functions``
reference docs for more.

Parameters:

    

**seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)")) – Integer used to seed Hail’s random number generator

hail.reset_global_randomness()[[source]](_modules/hail/context.html#reset_global_randomness)

    

Restore global randomness to initial state for test reproducibility.

hail.citation(_*_ , _bibtex
=False_)[[source]](_modules/hail/context.html#citation)

    

Generate a Hail citation.

Parameters:

    

**bibtex** (_bool_) – Generate a citation in BibTeX form.

Returns:

    

_str_

hail.version()[[source]](_modules/hail.html#version)

---

# BaseTable

_class _hail.table.BaseTable[[source]](_modules/hail/table.html#BaseTable)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

Base class for ```Table```,
[`GroupedTable`](hail.GroupedTable.html#hail.GroupedTable
"hail.table.GroupedTable"),
[`hail.matrixtable.MatrixTable`](hail.MatrixTable.html#hail.MatrixTable
"hail.matrixtable.MatrixTable"), and
[`hail.matrixtable.GroupedMatrixTable`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable
"hail.matrixtable.GroupedMatrixTable")

Attributes

Methods

---

# Table

_class _hail.Table[[source]](_modules/hail/table.html#Table)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Hail’s distributed implementation of a dataframe or SQL table.

Use [`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table") to read a table that was written with
`Table.write()`. Use `to_spark()` and `Table.from_spark()` to inter-operate
with PySpark’s [SQL](https://spark.apache.org/docs/latest/sql-programming-
guide.html) and [machine learning](https://spark.apache.org/docs/latest/ml-
guide.html) functionality.

Examples

The examples below use `table1` and `table2`, which are imported from text
files using [`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table").

    
    
    >>> table1 = hl.import_table('data/kt_example1.tsv', impute=True, key='ID')
    >>> table1.show()
    
    
    
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | M   |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | M   |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | F   |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | F   |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    
    
    
    >>> table2 = hl.import_table('data/kt_example2.tsv', impute=True, key='ID')
    >>> table2.show()
    
    
    
    +-------+-------+--------+
    |    ID |     A | B      |
    +-------+-------+--------+
    | int32 | int32 | str    |
    +-------+-------+--------+
    |     1 |    65 | cat    |
    |     2 |    72 | dog    |
    |     3 |    70 | mouse  |
    |     4 |    60 | rabbit |
    +-------+-------+--------+
    

Define new annotations:

    
    
    >>> height_mean_m = 68
    >>> height_sd_m = 3
    >>> height_mean_f = 65
    >>> height_sd_f = 2.5
    >>>
    >>> def get_z(height, sex):
    ...    return hl.if_else(sex == 'M',
    ...                     (height - height_mean_m) / height_sd_m,
    ...                     (height - height_mean_f) / height_sd_f)
    >>>
    >>> table1 = table1.annotate(height_z = get_z(table1.HT, table1.SEX))
    >>> table1 = table1.annotate_globals(global_field_1 = [1, 2, 3])
    

Filter rows of the table:

    
    
    >>> table2 = table2.filter(table2.B != 'rabbit')
    

Compute global aggregation statistics:

    
    
    >>> t1_stats = table1.aggregate(hl.struct(mean_c1 = hl.agg.mean(table1.C1),
    ...                                       mean_c2 = hl.agg.mean(table1.C2),
    ...                                       stats_c3 = hl.agg.stats(table1.C3)))
    >>> print(t1_stats)
    

Group by a field and aggregate to produce a new table:

    
    
    >>> table3 = (table1.group_by(table1.SEX)
    ...                 .aggregate(mean_height_data = hl.agg.mean(table1.HT)))
    >>> table3.show()
    

Join tables together inside an annotation expression:

    
    
    >>> table2 = table2.key_by('ID')
    >>> table1 = table1.annotate(B = table2[table1.ID].B)
    >>> table1.show()
    

Attributes

`globals` | Returns a struct expression including all global fields.  
---|---  
`key` | Row key struct.  
`row` | Returns a struct expression of all row-indexed fields, including keys.  
`row_value` | Returns a struct expression including all non-key row-indexed fields.  
  
Methods

`add_index` | Add the integer index of each row as a new row field.  
---|---  
`aggregate` | Aggregate over rows into a local value.  
`all` | Evaluate whether a boolean expression is true for all rows.  
`annotate` | Add new fields.  
`annotate_globals` | Add new global fields.  
`anti_join` | Filters the table to rows whose key does not appear in other.  
`any` | Evaluate whether a Boolean expression is true for at least one row.  
`cache` | Persist this table in memory.  
`checkpoint` | Checkpoint the table to disk by writing and reading.  
`collect` | Collect the rows of the table into a local list.  
`collect_by_key` | Collect values for each unique key into an array.  
`count` | Count the number of rows in the table.  
`describe` | Print information about the fields in the table.  
`distinct` | Deduplicate keys, keeping exactly one row for each unique key.  
`drop` | Drop fields from the table.  
`expand_types` | Expand complex types into structs and arrays.  
`explode` | Explode rows along a field of type array or set, copying the entire row for each element.  
`export` | Export to a text file.  
`filter` | Filter rows conditional on the value of each row's fields.  
`flatten` | Flatten nested structs.  
`from_pandas` | Create table from Pandas DataFrame  
`from_spark` | Convert PySpark SQL DataFrame to a table.  
`group_by` | Group by a new key for use with ```GroupedTable.aggregate()```.  
`head` | Subset table to first n rows.  
`index` | Expose the row values as if looked up in a dictionary, indexing with exprs.  
`index_globals` | Return this table's global variables for use in another expression context.  
`join` | Join two tables together.  
`key_by` | Key table by a new set of fields.  
`multi_way_zip_join` | Combine many tables in a zip join  
`n_partitions` | Returns the number of partitions in the table.  
`naive_coalesce` | Naively decrease the number of partitions.  
`order_by` | Sort by the specified fields, defaulting to ascending order.  
`parallelize` | Parallelize a local array of structs into a distributed table.  
`persist` | Persist this table in memory or on disk.  
`rename` | Rename fields of the table.  
`repartition` | Change the number of partitions.  
`sample` | Downsample the table by keeping each row with probability `p`.  
`select` | Select existing fields or create new fields by name, dropping the rest.  
`select_globals` | Select existing global fields or create new fields by name, dropping the rest.  
`semi_join` | Filters the table to rows whose key appears in other.  
`show` | Print the first few rows of the table to the console.  
`summarize` | Compute and print summary information about the fields in the table.  
`tail` | Subset table to last n rows.  
`take` | Collect the first n rows of the table into a local list.  
`to_matrix_table` | Construct a matrix table from a table in coordinate representation.  
`to_matrix_table_row_major` | Construct a matrix table from a table in row major representation.  
`to_pandas` | Converts this table to a Pandas DataFrame.  
`to_spark` | Converts this table to a Spark DataFrame.  
`transmute` | Add new fields and drop fields referenced.  
`transmute_globals` | Similar to `Table.annotate_globals()`, but drops referenced fields.  
`union` | Union the rows of multiple tables.  
`unpersist` | Unpersists this table from memory/disk.  
`write` | Write to disk.  
`write_many` | Write fields to distinct tables.  
  
add_index(_name ='idx'_)[[source]](_modules/hail/table.html#Table.add_index)

    

Add the integer index of each row as a new row field.

Examples

    
    
    >>> table_result = table1.add_index()
    >>> table_result.show()  
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |   idx |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int64 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |     1 |    65 | M   |     5 |     4 |     2 |    50 |     5 |     0 |
    |     2 |    72 | M   |     6 |     3 |     2 |    61 |     1 |     1 |
    |     3 |    70 | F   |     7 |     3 |    10 |    81 |    -5 |     2 |
    |     4 |    60 | F   |     8 |     2 |    11 |    90 |   -10 |     3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    

Notes

This method returns a table with a new field whose name is given by the name
parameter, with type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64"). The value of this field is the integer index of
each row, starting from 0. Methods that respect ordering (like `Table.take()`
or `Table.export()`) will return rows in order.

This method is also helpful for creating a unique integer index for rows of a
table so that more complex types can be encoded as a simple number for
performance reasons.

Parameters:

    

**name** (_str_) – Name of index field.

Returns:

    

`Table` – Table with a new index field.

aggregate(_expr_ , __localize
=True_)[[source]](_modules/hail/table.html#Table.aggregate)

    

Aggregate over rows into a local value.

Examples

Aggregate over rows:

    
    
    >>> table1.aggregate(hl.struct(fraction_male=hl.agg.fraction(table1.SEX == 'M'),
    ...                            mean_x=hl.agg.mean(table1.X)))
    Struct(fraction_male=0.5, mean_x=6.5)
    

Note

This method supports (and expects!) aggregation over rows.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expression.

Returns:

    

_any_ – Aggregated value dependent on expr.

all(_expr_)[[source]](_modules/hail/table.html#Table.all)

    

Evaluate whether a boolean expression is true for all rows.

Examples

Test whether C1 is greater than 5 in all rows of the table:

    
    
    >>> if table1.all(table1.C1 == 5):
    ...     print("All rows have C1 equal 5.")
    

Parameters:

    

**expr**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Expression to test.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

annotate(_** named_exprs_)[[source]](_modules/hail/table.html#Table.annotate)

    

Add new fields.

New Table fields may be defined in several ways:

  1. In terms of constant values. Every row will have the same value.

  2. In terms of other fields in the table.

  3. In terms of fields in other tables, this is called “joining”.

Examples

Consider this table:

    
    
    >>> ht = ht.drop('C1', 'C2', 'C3')
    >>> ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Add field Y containing the square of field X

    
    
    >>> ht = ht.annotate(Y = ht.X ** 2)
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+
    |    ID |    HT | SEX |     X |     Z |        Y |
    +-------+-------+-----+-------+-------+----------+
    | int32 | int32 | str | int32 | int32 |  float64 |
    +-------+-------+-----+-------+-------+----------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |
    +-------+-------+-----+-------+-------+----------+
    

Add multiple fields simultaneously:

    
    
    >>> ht = ht.annotate(
    ...     A = ht.X / 2,
    ...     B = ht.X + 21
    ... )
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+----------+-------+
    |    ID |    HT | SEX |     X |     Z |        Y |        A |     B |
    +-------+-------+-----+-------+-------+----------+----------+-------+
    | int32 | int32 | str | int32 | int32 |  float64 |  float64 | int32 |
    +-------+-------+-----+-------+-------+----------+----------+-------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 | 2.50e+00 |    26 |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 | 3.00e+00 |    27 |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 | 3.50e+00 |    28 |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 | 4.00e+00 |    29 |
    +-------+-------+-----+-------+-------+----------+----------+-------+
    

Add a new field computed from extant fields and a small dictionary:

    
    
    >>> py_height_description = {65: 'sixty-five', 72: 'seventy-two', 70: 'seventy', 60: 'sixty'}
    >>> hail_height_description = hl.literal(py_height_description)
    >>> ht = ht.annotate(HT_DESCRIPTION=hail_height_description[ht.HT])
    >>> ht.select('HT', 'HT_DESCRIPTION').show()
    +-------+-------+----------------+
    |    ID |    HT | HT_DESCRIPTION |
    +-------+-------+----------------+
    | int32 | int32 | str            |
    +-------+-------+----------------+
    |     1 |    65 | "sixty-five"   |
    |     2 |    72 | "seventy-two"  |
    |     3 |    70 | "seventy"      |
    |     4 |    60 | "sixty"        |
    +-------+-------+----------------+
    

Add fields from another table onto this table:

    
    
    >>> ht2 = table2
    >>> ht2 = ht2.key_by('ID')
    >>> ht2.show()
    
    
    
    >>> ht = ht.key_by('ID')
    >>> ht = ht.annotate(
    ...    A=ht2[ht.key].A,
    ...    B=ht2[ht.key].B,
    ... )
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |    ID |    HT | SEX |     X |     Z |        Y |     A | B        |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    | int32 | int32 | str | int32 | int32 |  float64 | int32 | str      |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |    65 | "cat"    |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |    72 | "dog"    |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |    70 | "mouse"  |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |    60 | "rabbit" |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    +----------------+
    | HT_DESCRIPTION |
    +----------------+
    | str            |
    +----------------+
    | "sixty-five"   |
    | "seventy-two"  |
    | "seventy"      |
    | "sixty"        |
    +----------------+
    

Instead of repeating all the fields from the other table, we may use Python’s
splat operator to indicate we want to copy all the non-key fields from the
other table:

    
    
    >>> ht = ht.annotate(**ht2[ht.key])
    >>> ht.show()
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |    ID |    HT | SEX |     X |     Z |        Y |     A | B        |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    | int32 | int32 | str | int32 | int32 |  float64 | int32 | str      |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    |     1 |    65 | "M" |     5 |     4 | 2.50e+01 |    65 | "cat"    |
    |     2 |    72 | "M" |     6 |     3 | 3.60e+01 |    72 | "dog"    |
    |     3 |    70 | "F" |     7 |     3 | 4.90e+01 |    70 | "mouse"  |
    |     4 |    60 | "F" |     8 |     2 | 6.40e+01 |    60 | "rabbit" |
    +-------+-------+-----+-------+-------+----------+-------+----------+
    +----------------+
    | HT_DESCRIPTION |
    +----------------+
    | str            |
    +----------------+
    | "sixty-five"   |
    | "seventy-two"  |
    | "seventy"      |
    | "sixty"        |
    +----------------+
    

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expressions for new fields.

Returns:

    

`Table` – Table with new fields.

annotate_globals(_**
named_exprs_)[[source]](_modules/hail/table.html#Table.annotate_globals)

    

Add new global fields.

Examples

Add a new global field:

    
    
    >>> ht = hl.utils.range_table(1)
    >>> ht = ht.annotate_globals(pops = ['EUR', 'AFR', 'EAS', 'SAS'])
    >>> ht.globals.show()
    +---------------------------+
    | <expr>.pops               |
    +---------------------------+
    | array<str>                |
    +---------------------------+
    | ["EUR","AFR","EAS","SAS"] |
    +---------------------------+
    

Global fields may be used to store metadata about an experiment:

    
    
    >>> ht = ht.annotate_globals(
    ...     study_name='HGDP+1kG',
    ...     release_date='2023-01-01'
    ... )
    

Note

This method does not support aggregation.

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expressions defining new global fields.

Returns:

    

`Table` – Table with new global field(s).

anti_join(_other_)[[source]](_modules/hail/table.html#Table.anti_join)

    

Filters the table to rows whose key does not appear in other.

Parameters:

    

**other** (`Table`) – Table with compatible key field(s).

Returns:

    

`Table`

Notes

The key type of the table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the table to keys not present in another table.

To restrict to keys present in other, use `semi_join()`.

Examples

    
    
    >>> table_result = table1.anti_join(table2)
    

It may be expensive to key the left-side table by the right-side key. In this
case, it is possible to implement an anti-join using a non-key field as
follows:

    
    
    >>> table_result = table1.filter(hl.is_missing(table2.index(table1['ID'])))
    

See also

`semi_join()`, `filter()`

any(_expr_)[[source]](_modules/hail/table.html#Table.any)

    

Evaluate whether a Boolean expression is true for at least one row.

Examples

Test whether C1 is equal to 5 any row in any row of the table:

    
    
    >>> if table1.any(table1.C1 == 5):
    ...     print("At least one row has C1 equal 5.")
    

Parameters:

    

**expr**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Boolean expression.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)") – `True` if the predicate evaluated for `True` for any row, otherwise
`False`.

cache()[[source]](_modules/hail/table.html#Table.cache)

    

Persist this table in memory.

Examples

Persist the table in memory:

    
    
    >>> table = table.cache() 
    

Notes

This method is an alias for `persist("MEMORY_ONLY")`.

Returns:

    

`Table` – Cached table.

checkpoint(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_, __read_if_exists =False_, __intervals =None_, __filter_intervals
=False_)[[source]](_modules/hail/table.html#Table.checkpoint)

    

Checkpoint the table to disk by writing and reading.

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

Returns:

    

`Table`

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

Notes

An alias for `write()` followed by
[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table"). It is possible to read the file at this path later
with [`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table").

Examples

    
    
    >>> table1 = table1.checkpoint('output/table_checkpoint.ht', overwrite=True)
    

collect(__localize =True_, _*_ , __timed
=False_)[[source]](_modules/hail/table.html#Table.collect)

    

Collect the rows of the table into a local list.

Examples

Collect a list of all X records:

    
    
    >>> all_xs = [row['X'] for row in table1.select(table1.X).collect()]
    

Notes

This method returns a list whose elements are of type
```Struct```. Fields of
these structs can be accessed similarly to fields on a table, using dot
methods (`struct.foo`) or string indexing (`struct['foo']`).

Warning

Using this method can cause out of memory errors. Only collect small tables.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of ```Struct```
– List of rows.

collect_by_key(_name
='values'_)[[source]](_modules/hail/table.html#Table.collect_by_key)

    

Collect values for each unique key into an array.

Note

Requires a keyed table.

Examples

    
    
    >>> t1 = hl.Table.parallelize([
    ...     {'t': 'foo', 'x': 4, 'y': 'A'},
    ...     {'t': 'bar', 'x': 2, 'y': 'B'},
    ...     {'t': 'bar', 'x': -3, 'y': 'C'},
    ...     {'t': 'quam', 'x': 0, 'y': 'D'}],
    ...     hl.tstruct(t=hl.tstr, x=hl.tint32, y=hl.tstr),
    ...     key='t')
    
    
    
    >>> t1.show()
    +--------+-------+-----+
    | t      |     x | y   |
    +--------+-------+-----+
    | str    | int32 | str |
    +--------+-------+-----+
    | "bar"  |     2 | "B" |
    | "bar"  |    -3 | "C" |
    | "foo"  |     4 | "A" |
    | "quam" |     0 | "D" |
    +--------+-------+-----+
    
    
    
    >>> t1.collect_by_key().show()
    +--------+---------------------------------+
    | t      | values                          |
    +--------+---------------------------------+
    | str    | array<struct{x: int32, y: str}> |
    +--------+---------------------------------+
    | "bar"  | [(2,"B"),(-3,"C")]              |
    | "foo"  | [(4,"A")]                       |
    | "quam" | [(0,"D")]                       |
    +--------+---------------------------------+
    

Notes

The order of the values array is not guaranteed.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Field name for all values per key.

Returns:

    

`Table`

count()[[source]](_modules/hail/table.html#Table.count)

    

Count the number of rows in the table.

Examples

Count the number of rows in a table loaded from ‘data/kt_example1.tsv’. Each
line of the TSV becomes one row in the Hail Table.

    
    
    >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
    >>> ht.count()
    4
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – The number of rows in the table.

describe(_handler= <built-in function print>_, _*_ ,
_widget=False_)[[source]](_modules/hail/table.html#Table.describe)

    

Print information about the fields in the table.

Note

The widget argument is **experimental**.

Parameters:

    

  * **handler** (_Callable[[str], None]_) – Handler function for returned string.

  * **widget** (_bool_) – Create an interactive IPython widget.

distinct()[[source]](_modules/hail/table.html#Table.distinct)

    

Deduplicate keys, keeping exactly one row for each unique key.

Note

Requires a keyed table.

Examples

    
    
    >>> t1 = hl.Table.parallelize([
    ...     {'a': 'foo', 'b': 1},
    ...     {'a': 'bar', 'b': 5},
    ...     {'a': 'bar', 'b': 2}],
    ...     hl.tstruct(a=hl.tstr, b=hl.tint32),
    ...     key='a')
    
    
    
    >>> t1.show()
    +-------+-------+
    | a     |     b |
    +-------+-------+
    | str   | int32 |
    +-------+-------+
    | "bar" |     5 |
    | "bar" |     2 |
    | "foo" |     1 |
    +-------+-------+
    
    
    
    >>> t1.distinct().show()
    +-------+-------+
    | a     |     b |
    +-------+-------+
    | str   | int32 |
    +-------+-------+
    | "bar" |     5 |
    | "foo" |     1 |
    +-------+-------+
    

Notes

The row chosen per distinct key is not guaranteed.

Returns:

    

`Table`

drop(_* exprs_)[[source]](_modules/hail/table.html#Table.drop)

    

Drop fields from the table.

Examples

Drop fields C1 and C2 using strings:

    
    
    >>> table_result = table1.drop('C1', 'C2')
    

Drop fields C1 and C2 using field references:

    
    
    >>> table_result = table1.drop(table1.C1, table1.C2)
    

Drop a list of fields:

    
    
    >>> fields_to_drop = ['C1', 'C2']
    >>> table_result = table1.drop(*fields_to_drop)
    

Notes

This method can be used to drop global or row-indexed fields. The arguments
can be either strings (`'field'`), or top-level field references
(`table.field` or `table['field']`).

Parameters:

    

**exprs** (varargs of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or [`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Names of fields to drop or field reference
expressions.

Returns:

    

`Table` – Table without specified fields.

expand_types()[[source]](_modules/hail/table.html#Table.expand_types)

    

Expand complex types into structs and arrays.

Examples

    
    
    >>> table_result = table1.expand_types()
    

Notes

Expands the following types: [`tlocus`](types.html#hail.expr.types.tlocus
"hail.expr.types.tlocus"), [`tinterval`](types.html#hail.expr.types.tinterval
"hail.expr.types.tinterval"), [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset"), [`tdict`](types.html#hail.expr.types.tdict
"hail.expr.types.tdict"), [`ttuple`](types.html#hail.expr.types.ttuple
"hail.expr.types.ttuple").

The only types that will remain after this method are:
```tbool```,
```tint32```,
```tint64```,
```tfloat64```,
```tfloat32```,
```tarray```,
```tstruct```.

Note, expand_types always returns an unkeyed table.

Returns:

    

`Table` – Expanded table.

explode(_field_ , _name
=None_)[[source]](_modules/hail/table.html#Table.explode)

    

Explode rows along a field of type array or set, copying the entire row for
each element.

Examples

people_table is a `Table` with three fields: Name, Age and Children.

    
    
    >>> people_table.show()
    +------------+-------+--------------------------+
    | Name       |   Age | Children                 |
    +------------+-------+--------------------------+
    | str        | int32 | array<str>               |
    +------------+-------+--------------------------+
    | "Alice"    |    34 | ["Dave","Ernie","Frank"] |
    | "Bob"      |    51 | ["Gaby","Helen"]         |
    | "Caroline" |    10 | []                       |
    +------------+-------+--------------------------+
    

`Table.explode()` can be used to produce a distinct row for each element in
the Children field:

    
    
    >>> exploded = people_table.explode('Children')
    >>> exploded.show() 
    +---------+-------+----------+
    | Name    |   Age | Children |
    +---------+-------+----------+
    | str     | int32 | str      |
    +---------+-------+----------+
    | "Alice" |    34 | "Dave"   |
    | "Alice" |    34 | "Ernie"  |
    | "Alice" |    34 | "Frank"  |
    | "Bob"   |    51 | "Gaby"   |
    | "Bob"   |    51 | "Helen"  |
    +---------+-------+----------+
    

The name parameter can be used to produce more appropriate field names:

    
    
    >>> exploded = people_table.explode('Children', name='Child')
    >>> exploded.show() 
    +---------+-------+---------+
    | Name    |   Age | Child   |
    +---------+-------+---------+
    | str     | int32 | str     |
    +---------+-------+---------+
    | "Alice" |    34 | "Dave"  |
    | "Alice" |    34 | "Ernie" |
    | "Alice" |    34 | "Frank" |
    | "Bob"   |    51 | "Gaby"  |
    | "Bob"   |    51 | "Helen" |
    +---------+-------+---------+
    

Notes

Each row is copied for each element of field. The explode operation unpacks
the elements in a field of type `array` or `set` into its own row. If an empty
`array` or `set` is exploded, the entire row is removed from the table. In the
example above, notice that the name “Caroline” is not found in the exploded
table.

Missing arrays or sets are treated as empty.

Currently, the name argument may not be used if field is not a top-level field
of the table (e.g. name may be used with `ht.foo` but not `ht.foo.bar`).

Parameters:

    

  * **field** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Top-level field name or expression.

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or None) – If not None, rename the exploded field to name.

Returns:

    

`Table`

export(_output_ , _types_file =None_, _header =True_, _parallel =None_,
_delimiter ='\t'_)[[source]](_modules/hail/table.html#Table.export)

    

Export to a text file.

Examples

Export to a tab-separated file:

    
    
    >>> table1.export('output/table1.tsv.bgz')
    

Note

It is highly recommended to export large files with a `.bgz` extension, which
will use a block gzipped compression codec. These files can be read natively
with any Hail method, as well as with Python’s `gzip.open` and R’s
`read.table`.

Nested structures will be exported as JSON. In order to export nested struct
fields as separate fields in the resulting table, use `flatten()` first.

Warning

Do not export to a path that is being read from in the same pipeline.

See also

`flatten()`, `write()`

Parameters:

    

  * **output** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – URI at which to write exported file.

  * **types_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – URI at which to write file containing field type information.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Include a header in the file.

  * **parallel** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), optional) – If None, a single file is produced, otherwise a folder of file shards is produced. If ‘separate_header’, the header file is output separately from the file shards. If ‘header_per_shard’, each file shard has a header. If set to None the export will be slower.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Field delimiter.

filter(_expr_ , _keep
=True_)[[source]](_modules/hail/table.html#Table.filter)

    

Filter rows conditional on the value of each row’s fields.

Note

Hail will can read much less data if a Table filter condition references the
key field and the Table is stored in Hail native format (i.e. read using
[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table"), _not_
[`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table")). In other words: filtering on the key will make
a pipeline faster by reading fewer rows. This optimization is prevented by
certain operations appearing between a
[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table") and a `filter()`. For example, a key_by and
group_by, both force reading all the data.

Suppose we previously `write()` a Hail Table with one million rows keyed by a
field called idx. If we filter this table to one value of idx, the pipeline
will be fast because we read only the rows that have that value of idx:

    
    
    >>> ht = hl.read_table('large-table.ht')  
    >>> ht = ht.filter(ht.idx == 5)  
    

This also works with inequality conditions:

    
    
    >>> ht = hl.read_table('large-table.ht')  
    >>> ht = ht.filter(ht.idx <= 5)  
    

Examples

Consider this table:

    
    
    >>> ht = ht.drop('C1', 'C2', 'C3')
    >>> ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Keep rows where `Z` is 3:

    
    
    >>> filtered_ht = ht.filter(ht.Z == 3)
    >>> filtered_ht.show()
    

ID | HT | SEX | X | Z  
---|---|---|---|---  
int32 | int32 | str | int32 | int32  
2 3 | 72 70 | “M” “F” | 6 7 | 3 3  
  
Remove rows where `Z` is 3:

    
    
    >>> filtered_ht = ht.filter(ht.Z == 3, keep=False)
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Keep rows where X is less than 7 and Z is greater than 2:

    
    
    >>> filtered_ht = ht.filter(hl.all(
    ...     ht.X < 7,
    ...     ht.Z > 2
    ... ))
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    +-------+-------+-----+-------+-------+
    

Keep rows where X is less than 7 or Z is greater than 2:

    
    
    >>> filtered_ht = ht.filter(hl.any(
    ...     ht.X < 7,
    ...     ht.Z > 2
    ... ))
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    +-------+-------+-----+-------+-------+
    

Keep “M” rows where `HT` is less than 72 and “F” rows where `HT` is less than
65:

    
    
    >>> filtered_ht = ht.filter(
    ...     hl.if_else(
    ...         ht.SEX == "M",
    ...         ht.HT < 72,
    ...         ht.HT < 65
    ...     )
    ... )
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Notice that if the condition evaluates to missing, the row is _always_ removed
regardless of the setting of keep:

    
    
    >>> ht2 = ht
    >>> ht2 = ht.annotate(X = hl.or_missing(ht.X != 5, ht.X))
    >>> ht2.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     1 |    65 | "M" |    NA |     4 |
    |     2 |    72 | "M" |     6 |     3 |
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    >>> filtered_ht = ht2.filter(ht2.X < 7, keep=True)
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     2 |    72 | "M" |     6 |     3 |
    +-------+-------+-----+-------+-------+
    >>> filtered_ht = ht2.filter(ht2.X < 7, keep=False)
    >>> filtered_ht.show()
    +-------+-------+-----+-------+-------+
    |    ID |    HT | SEX |     X |     Z |
    +-------+-------+-----+-------+-------+
    | int32 | int32 | str | int32 | int32 |
    +-------+-------+-----+-------+-------+
    |     3 |    70 | "F" |     7 |     3 |
    |     4 |    60 | "F" |     8 |     2 |
    +-------+-------+-----+-------+-------+
    

Notes

The expression expr will be evaluated for every row of the table. If keep is
`True`, then rows where expr evaluates to `True` will be kept (the filter
removes the rows where the predicate evaluates to `False`). If keep is
`False`, then rows where expr evaluates to `True` will be removed (the filter
keeps the rows where the predicate evaluates to `False`).

Warning

When expr evaluates to missing, the row will be removed regardless of keep.

Note

This method does not support aggregation.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep rows where expr is true.

Returns:

    

`Table` – Filtered table.

flatten()[[source]](_modules/hail/table.html#Table.flatten)

    

Flatten nested structs.

Examples

Flatten table:

    
    
    >>> table_result = table1.flatten()
    

Notes

Consider a table with signature

    
    
    a: struct{
        p: int32,
        q: str
    },
    b: int32,
    c: struct{
        x: str,
        y: array<struct{
            y: str,
            z: str
        }>
    }
    

and key `a`. The result of flatten is

    
    
    a.p: int32
    a.q: str
    b: int32
    c.x: str
    c.y: array<struct{
        y: str,
        z: str
    }>
    

with key `a.p, a.q`.

Note, structures inside collections like arrays or sets will not be flattened.

Note, the result of flatten is always unkeyed.

Warning

Flattening a table will produces fields that cannot be referenced using the
`table.<field>` syntax, e.g. “a.b”. Reference these fields using square
bracket lookups: `table['a.b']`.

Returns:

    

`Table` – Table with a flat schema (no struct fields).

_static _from_pandas(_df_ , _key
=[]_)[[source]](_modules/hail/table.html#Table.from_pandas)

    

Create table from Pandas DataFrame

Examples

    
    
    >>> t = hl.Table.from_pandas(df) 
    

Parameters:

    

  * **df** ([`pandas.DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame "\(in pandas v2.3.0\)")) – Pandas DataFrame.

  * **key** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Key fields.

Returns:

    

`Table`

_static _from_spark(_df_ , _key
=[]_)[[source]](_modules/hail/table.html#Table.from_spark)

    

Convert PySpark SQL DataFrame to a table.

Examples

    
    
    >>> t = Table.from_spark(df) 
    

Notes

Spark SQL data types are converted to Hail types as follows:

    
    
    BooleanType => :py``.tbool``
    IntegerType => :py``.tint32``
    LongType => :py``.tint64``
    FloatType => :py``.tfloat32``
    DoubleType => :py``.tfloat64``
    StringType => :py``.tstr``
    BinaryType => ``.TBinary``
    ArrayType => ``.tarray``
    StructType => ``.tstruct``
    

Unlisted Spark SQL data types are currently unsupported.

Parameters:

    

  * **df** ([`pyspark.sql.DataFrame`](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrame.html#pyspark.sql.DataFrame "\(in PySpark vmaster\)")) – PySpark DataFrame.

  * **key** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Key fields.

Returns:

    

`Table` – Table constructed from the Spark SQL DataFrame.

_property _globals

    

Returns a struct expression including all global fields.

Examples

The data type of the globals struct:

    
    
    >>> table1.globals.dtype
    dtype('struct{global_field_1: int32, global_field_2: int32}')
    

The number of global fields:

    
    
    >>> len(table1.globals)
    2
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all global fields.

group_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/table.html#Table.group_by)

    

Group by a new key for use with
[`GroupedTable.aggregate()`](hail.GroupedTable.html#hail.GroupedTable.aggregate
"hail.GroupedTable.aggregate").

Examples

Compute the mean value of X and the sum of Z per unique ID:

    
    
    >>> table_result = (table1.group_by(table1.ID)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    

Group by a height bin and compute sex ratio per bin:

    
    
    >>> table_result = (table1.group_by(height_bin = table1.HT // 20)
    ...                       .aggregate(fraction_female = hl.agg.fraction(table1.SEX == 'F')))
    

Notes

This function is always followed by
[`GroupedTable.aggregate()`](hail.GroupedTable.html#hail.GroupedTable.aggregate
"hail.GroupedTable.aggregate"). Follow the link for documentation on the
aggregation step.

Note

**Using group_by**

**group_by** and its sibling methods
([`MatrixTable.group_rows_by()`](hail.MatrixTable.html#hail.MatrixTable.group_rows_by
"hail.MatrixTable.group_rows_by") and
[`MatrixTable.group_cols_by()`](hail.MatrixTable.html#hail.MatrixTable.group_cols_by
"hail.MatrixTable.group_cols_by")) accept both variable-length (`f(x, y, z)`)
and keyword (`f(a=x, b=y, c=z)`) arguments.

Variable-length arguments can be either strings or expressions that reference
a (possibly nested) field of the table. Keyword arguments can be arbitrary
expressions.

**The following three usages are all equivalent** , producing a
```GroupedTable```
grouped by fields C1 and C2 of table1.

First, variable-length string arguments:

    
    
    >>> table_result = (table1.group_by('C1', 'C2')
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Second, field reference variable-length arguments:

    
    
    >>> table_result = (table1.group_by(table1.C1, table1.C2)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Last, expression keyword arguments:

    
    
    >>> table_result = (table1.group_by(C1 = table1.C1, C2 = table1.C2)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Additionally, the variable-length argument syntax also permits nested field
references. Given the following struct field s:

    
    
    >>> table3 = table1.annotate(s = hl.struct(x=table1.X, z=table1.Z))
    

The following two usages are equivalent, grouping by one field, x:

    
    
    >>> table_result = (table3.group_by(table3.s.x)
    ...                       .aggregate(meanX = hl.agg.mean(table3.X)))
    
    
    
    >>> table_result = (table3.group_by(x = table3.s.x)
    ...                       .aggregate(meanX = hl.agg.mean(table3.X)))
    

The keyword argument syntax permits arbitrary expressions:

    
    
    >>> table_result = (table1.group_by(foo=table1.X ** 2 + 1)
    ...                       .aggregate(meanZ = hl.agg.mean(table1.Z)))
    

These syntaxes can be mixed together, with the stipulation that all keyword
arguments must come at the end due to Python language restrictions.

    
    
    >>> table_result = (table1.group_by(table1.C1, 'C2', height_bin = table1.HT // 20)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X)))
    

Note

This method does not support aggregation in key expressions.

Parameters:

    

  * **exprs** (varargs of type str or ```Expression```) – Field names or field reference expressions.

  * **named_exprs** (keyword args of type ```Expression```) – Field names and expressions to compute them.

Returns:

    

```GroupedTable```
– Grouped table; use
[`GroupedTable.aggregate()`](hail.GroupedTable.html#hail.GroupedTable.aggregate
"hail.GroupedTable.aggregate") to complete the aggregation.

head(_n_)[[source]](_modules/hail/table.html#Table.head)

    

Subset table to first n rows.

Examples

Subset to the first three rows:

    
    
    >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
    >>> ht.head(3).show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Notes

The number of partitions in the new table is equal to the number of partitions
containing the first n rows.

Parameters:

    

**n** (_int_) – Number of rows to include.

Returns:

    

`Table` – Table limited to the first n rows.

index(_* exprs_, _all_matches
=False_)[[source]](_modules/hail/table.html#Table.index)

    

Expose the row values as if looked up in a dictionary, indexing with exprs.

Examples

In the example below, both table1 and table2 are keyed by one field ID of type
`int`.

    
    
    >>> table_result = table1.select(B = table2.index(table1.ID).B)
    >>> table_result.B.show()
    +-------+----------+
    |    ID | B        |
    +-------+----------+
    | int32 | str      |
    +-------+----------+
    |     1 | "cat"    |
    |     2 | "dog"    |
    |     3 | "mouse"  |
    |     4 | "rabbit" |
    +-------+----------+
    

Using key as the sole index expression is equivalent to passing all key fields
individually:

    
    
    >>> table_result = table1.select(B = table2.index(table1.key).B)
    

It is also possible to use non-key fields or expressions as the index
expressions:

    
    
    >>> table_result = table1.select(B = table2.index(table1.C1 % 4).B)
    >>> table_result.show()
    +-------+---------+
    |    ID | B       |
    +-------+---------+
    | int32 | str     |
    +-------+---------+
    |     1 | "dog"   |
    |     2 | "dog"   |
    |     3 | "dog"   |
    |     4 | "mouse" |
    +-------+---------+
    

Notes

`Table.index()` is used to expose one table’s fields for use in expressions
involving the another table or matrix table’s fields. The result of the method
call is a struct expression that is usable in the same scope as exprs, just as
if exprs were used to look up values of the table in a dictionary.

The type of the struct expression is the same as the indexed table’s
`row_value()` (the key fields are removed, as they are available in the form
of the index expressions).

Note

There is a shorthand syntax for `Table.index()` using square brackets (the
Python `__getitem__` syntax). This syntax is preferred.

    
    
    >>> table_result = table1.select(B = table2[table1.ID].B)
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Index expressions.

  * **all_matches** (_bool_) – Experimental. If `True`, value of expression is array of all matches.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

index_globals()[[source]](_modules/hail/table.html#Table.index_globals)

    

Return this table’s global variables for use in another expression context.

Examples

    
    
    >>> table_result = table2.annotate(C = table2.A * table1.index_globals().global_field_1)
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

join(_right_ , _how='inner'_ , __mangle= <function Table.<lambda>>_,
__join_key=None_)[[source]](_modules/hail/table.html#Table.join)

    

Join two tables together.

Examples

Join table1 to table2 to produce table_joined:

    
    
    >>> table_joined = table1.key_by('ID').join(table2.key_by('ID'))
    

Notes

Tables are joined at rows whose key fields have equal values. Missing values
never match. The inclusion of a row with no match in the opposite table
depends on the join type:

  * **inner** – Only rows with a matching key in the opposite table are included in the resulting table.

  * **left** – All rows from the left table are included in the resulting table. If a row in the left table has no match in the right table, then the fields derived from the right table will be missing.

  * **right** – All rows from the right table are included in the resulting table. If a row in the right table has no match in the left table, then the fields derived from the left table will be missing.

  * **outer** – All rows are included in the resulting table. If a row in the right table has no match in the left table, then the fields derived from the left table will be missing. If a row in the right table has no match in the left table, then the fields derived from the left table will be missing.

Both tables must have the same number of keys and the corresponding types of
each key must be the same (order matters), but the key names can be different.
For example, if table1 is keyed by fields `['a', 'b']`, both of type `int32`,
and table2 is keyed by fields `['c', 'd']`, both of type `int32`, then the two
tables can be joined (their rows will be joined where `table1.a == table2.c`
and `table1.b == table2.d`).

The key fields and order from the left table are preserved, while the key
fields from the right table are not present in the result.

Note

These join methods implement a traditional [Cartesian
product](https://en.wikipedia.org/wiki/Cartesian_product) join, and the number
of records in the resulting table can be larger than the number of records on
the left or right if duplicate keys are present.

Parameters:

    

  * **right** (`Table`) – Table to join.

  * **how** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Join type. One of “inner”, “left”, “right”, “outer”

Returns:

    

`Table` – Joined table.

_property _key

    

Row key struct.

Examples

List of key field names:

    
    
    >>> list(table1.key)
    ['ID']
    

Number of key fields:

    
    
    >>> len(table1.key)
    1
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

key_by(_* keys_, _**
named_keys_)[[source]](_modules/hail/table.html#Table.key_by)

    

Key table by a new set of fields.

Table keys control both the order of the rows in the table and the ability to
join or annotate one table with the information in another table.

Examples

Consider a simple unkeyed table. Its rows appear are guaranteed to appear in
the same order as they were in the source text file.

    
    
    >>> ht = hl.import_table('data/kt_example1.tsv', impute=True)
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Changing the key forces the rows to appear in ascending order. For this
reason, `key_by()` is a relatively expensive operation. It must sort the
entire dataset.

    
    
    >>> ht = ht.key_by('HT')
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Suppose that ht represents some human subjects in an experiment. We might need
to combine sample metadata from ht with sample metadata from another source.
For example:

    
    
    >>> ht2 = hl.import_table('data/kt_example2.tsv', impute=True)
    >>> ht2 = ht2.key_by('ID')
    >>> ht2.show()
    +-------+-------+----------+
    |    ID |     A | B        |
    +-------+-------+----------+
    | int32 | int32 | str      |
    +-------+-------+----------+
    |     1 |    65 | "cat"    |
    |     2 |    72 | "dog"    |
    |     3 |    70 | "mouse"  |
    |     4 |    60 | "rabbit" |
    +-------+-------+----------+
    >>> combined_ht = ht
    >>> combined_ht = combined_ht.key_by('ID')
    >>> combined_ht = combined_ht.annotate(favorite_pet = ht2[combined_ht.key].B)
    >>> combined_ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 | favorite_pet |
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | str          |
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 | "cat"        |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 | "dog"        |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 | "mouse"      |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 | "rabbit"     |
    +-------+-------+-----+-------+-------+-------+-------+-------+--------------+
    

Hail supports compound keys which enforce a dictionary ordering on the rows of
the Table.

    
    
    >>> ht = ht.key_by('SEX', 'HT')
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

A key may also be shortened by removing some fields. The ordering of two rows
with the same key is undefined. You should not rely on them appearing in any
particular order.

    
    
    >>> ht = ht.key_by('SEX')
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Key fields may also be a complex expression:

    
    
    >>> ht = ht.key_by(C4 = ht.X + ht.Z)
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |    C4 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |     9 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |     9 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |    10 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |    10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    

The key can be “removed” or set to the empty key. The ordering of the rows in
a table without a key is undefined.

    
    
    >>> ht = ht.key_by()
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |    C4 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |     9 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |     9 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |    10 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |    10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------+
    

Notes

This method is used to specify all the fields of a new row key. The old key
fields may be overwritten by newly-assigned fields, as described in
`Table.annotate()`. If not overwritten, they are preserved as non-key fields.

See `Table.select()` for more information about how to define new key fields.

Parameters:

    

**keys** (varargs of type
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Field(s) to key by.

Returns:

    

`Table` – Table with a new key.

_static _multi_way_zip_join(_tables_ , _data_field_name_ ,
_global_field_name_)[[source]](_modules/hail/table.html#Table.multi_way_zip_join)

    

Combine many tables in a zip join

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Notes

The row type of the returned table is a struct with the key fields, and one
extra field, data_field_name, which is an array of structs with the non key
fields, one per input. The array elements are missing if their corresponding
input had no row with that key or possibly if there is another input with more
rows with that key than the corresponding input.

The global type of the returned table is an array of structs of the global
type of all of the inputs.

The types for every input must be identical, not merely compatible, including
the keys.

A zip join is similar to an outer join however rows are not duplicated to
create the full Cartesian product of duplicate keys. Instead, there is exactly
one entry in some data_field_name array for every row in the inputs.

The `multi_way_zip_join()` method assumes that inputs have distinct keys. If
any input has duplicate keys, the row value that is included in the result
array for that key is undefined.

Parameters:

    

  * **tables** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of `Table`) – A list of tables to combine

  * **data_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the resulting data field

  * **global_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the resulting global field

n_partitions()[[source]](_modules/hail/table.html#Table.n_partitions)

    

Returns the number of partitions in the table.

Examples

Range tables can be constructed with an explicit number of partitions:

    
    
    >>> ht = hl.utils.range_table(100, n_partitions=10)
    >>> ht.n_partitions()
    10
    

Small files are often imported with one partition:

    
    
    >>> ht2 = hl.import_table('data/coordinate_matrix.tsv', impute=True)
    >>> ht2.n_partitions()
    1
    

The min_partitions argument to
[`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table") forces more partitions, but it can produce empty
partitions. Empty partitions do not affect correctness but introduce
unnecessary extra bookkeeping that slows down the pipeline.

    
    
    >>> ht2 = hl.import_table('data/coordinate_matrix.tsv', impute=True, min_partitions=10)
    >>> ht2.n_partitions()
    10
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Number of partitions.

naive_coalesce(_max_partitions_)[[source]](_modules/hail/table.html#Table.naive_coalesce)

    

Naively decrease the number of partitions.

Example

Naively repartition to 10 partitions:

    
    
    >>> table_result = table1.naive_coalesce(10)
    

Warning

`naive_coalesce()` simply combines adjacent partitions to achieve the desired
number. It does not attempt to rebalance, unlike `repartition()`, so it can
produce a heavily unbalanced dataset. An unbalanced dataset can be inefficient
to operate on because the work is not evenly distributed across partitions.

Parameters:

    

**max_partitions** (_int_) – Desired number of partitions. If the current
number of partitions is less than or equal to max_partitions, do nothing.

Returns:

    

`Table` – Table with at most max_partitions partitions.

order_by(_* exprs_)[[source]](_modules/hail/table.html#Table.order_by)

    

Sort by the specified fields, defaulting to ascending order. Will unkey the
table if it is keyed.

Examples

Let’s assume we have a field called HT in our table.

By default, ascending order is used:

    
    
    >>> sorted_table = table1.order_by(table1.HT)
    
    
    
    >>> sorted_table = table1.order_by('HT')
    

You can sort in ascending order explicitly:

    
    
    >>> sorted_table = table1.order_by(hl.asc(table1.HT))
    
    
    
    >>> sorted_table = table1.order_by(hl.asc('HT'))
    

Tables can be sorted by field descending order as well:

    
    
    >>> sorted_table = table1.order_by(hl.desc(table1.HT))
    
    
    
    >>> sorted_table = table1.order_by(hl.desc('HT'))
    

Tables can also be sorted on multiple fields:

    
    
    >>> sorted_table = table1.order_by(hl.desc('HT'), hl.asc('SEX'))
    

Notes

Missing values are sorted after non-missing values. When multiple fields are
passed, the table will be sorted first by the first argument, then the second,
etc.

Note

This method unkeys the table.

Parameters:

    

**exprs** (varargs of ```asc()```,
```desc()```,
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"), or
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Fields to sort by.

Returns:

    

`Table` – Table sorted by the given fields.

_classmethod _parallelize(_rows_ , _schema =None_, _key =None_, _n_partitions
=None_, _*_ , _partial_type =None_, _globals
=None_)[[source]](_modules/hail/table.html#Table.parallelize)

    

Parallelize a local array of structs into a distributed table.

Examples

Parallelize a list of dictionaries into a Hail Table. The fields of the
dictionary become the fields of the Table. The schema should always be a
```tstruct```
whose fields correspond to the dictionaries’ fields.

    
    
    >>> t = hl.Table.parallelize(
    ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    ...     schema=hl.tstruct(a=hl.tint, b=hl.tint)
    ... )
    >>> t.show()
    +-------+-------+
    |     a |     b |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     5 |    10 |
    |     0 |   200 |
    +-------+-------+
    

The key parameter sets the key of the Table. Notice that the order of the rows
changes, because the rows of a Table are always appear in ascending order of
the key.

    
    
    >>> t = hl.Table.parallelize(
    ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    ...     schema=hl.tstruct(a=hl.tint, b=hl.tint),
    ...     key='a'
    ... )
    >>> t.show()
    +-------+-------+
    |     a |     b |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |   200 |
    |     5 |    10 |
    +-------+-------+
    

You may also elide schema entirely and let Hail guess the type. The list
elements must either be Hail [`Struct`](utils/index.html#hail.utils.Struct
"hail.utils.Struct") or
[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)") s.

    
    
    >>> t = hl.Table.parallelize(
    ...     [{'a': 5, 'b': 10}, {'a': 0, 'b': 200}],
    ...     key='a'
    ... )
    >>> t.show()
    +-------+-------+
    |     a |     b |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |   200 |
    |     5 |    10 |
    +-------+-------+
    

You may also specify only a handful of types in partial_type. Hail will
automatically deduce the types of the other fields. Hail _cannot_ deduce the
type of a field which only contains empty arrays (the element type is
unspecified), so we specify the type of labels explicitly.

    
    
    >>> dictionaries = [
    ...     {"number":10038,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794}, "milestone":None,"labels":[]},
    ...     {"number":10037,"state":"open","user":{"login":"daniel-goldstein","site_admin":False,"id":24440116},"milestone":None,"labels":[]},
    ...     {"number":10036,"state":"open","user":{"login":"jigold","site_admin":False,"id":1693348},"milestone":None,"labels":[]},
    ...     {"number":10035,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794},"milestone":None,"labels":[]},
    ...     {"number":10033,"state":"open","user":{"login":"tpoterba","site_admin":False,"id":10562794},"milestone":None,"labels":[]},
    ... ]
    >>> t = hl.Table.parallelize(
    ...     dictionaries,
    ...     partial_type={"milestone": hl.tstr, "labels": hl.tarray(hl.tstr)}
    ... )
    >>> t.show()
    +--------+--------+--------------------+-----------------+----------+
    | number | state  | user.login         | user.site_admin |  user.id |
    +--------+--------+--------------------+-----------------+----------+
    |  int32 | str    | str                |            bool |    int32 |
    +--------+--------+--------------------+-----------------+----------+
    |  10038 | "open" | "tpoterba"         |           False | 10562794 |
    |  10037 | "open" | "daniel-goldstein" |           False | 24440116 |
    |  10036 | "open" | "jigold"           |           False |  1693348 |
    |  10035 | "open" | "tpoterba"         |           False | 10562794 |
    |  10033 | "open" | "tpoterba"         |           False | 10562794 |
    +--------+--------+--------------------+-----------------+----------+
    +-----------+------------+
    | milestone | labels     |
    +-----------+------------+
    | str       | array<str> |
    +-----------+------------+
    | NA        | []         |
    | NA        | []         |
    | NA        | []         |
    | NA        | []         |
    | NA        | []         |
    +-----------+------------+
    

Parallelizing with a specified number of partitions:

    
    
    >>> rows = [ {'a': i} for i in range(100) ]
    >>> ht = hl.Table.parallelize(rows, n_partitions=10)
    >>> ht.n_partitions()
    10
    >>> ht.count()
    100
    

Parallelizing with some global information:

    
    
    >>> rows = [ {'a': i} for i in range(5) ]
    >>> ht = hl.Table.parallelize(rows, globals=hl.Struct(global_value=3))
    >>> ht.aggregate(hl.agg.sum(ht.global_value * ht.a))
    30
    

Warning

Parallelizing very large local arrays will be slow.

Parameters:

    

  * **rows** – List of row values, or expression of type `array<struct{...}>`.

  * **schema** (str or a hail type (see ``Types``), optional) – Value type.

  * **key** (_Union[str, List[str]]], optional_) – Key field(s).

  * **n_partitions** (_int, optional_)

  * **partial_type** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)"), optional) – A value type which may elide fields or have `None` in arbitrary places. The partial type is used by hail where the type cannot be imputed.

  * **globals** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to `any` or ```StructExpression```, optional) – A dict or struct{..} containing supplementary global data.

Returns:

    

`Table` – A distributed Hail table created from the local collection of rows.

persist(_storage_level
='MEMORY_AND_DISK'_)[[source]](_modules/hail/table.html#Table.persist)

    

Persist this table in memory or on disk.

Examples

Persist the table to both memory and disk:

    
    
    >>> table = table.persist() 
    

Notes

The `Table.persist()` and `Table.cache()` methods store the current table on
disk or in memory temporarily to avoid redundant computation and improve the
performance of Hail pipelines. This method is not a substitution for
`Table.write()`, which stores a permanent file.

Most users should use the “MEMORY_AND_DISK” storage level. See the [Spark
documentation](http://spark.apache.org/docs/latest/programming-guide.html#rdd-
persistence) for a more in-depth discussion of persisting data.

Parameters:

    

**storage_level** (_str_) – Storage level. One of: NONE, DISK_ONLY,
DISK_ONLY_2, MEMORY_ONLY, MEMORY_ONLY_2, MEMORY_ONLY_SER, MEMORY_ONLY_SER_2,
MEMORY_AND_DISK, MEMORY_AND_DISK_2, MEMORY_AND_DISK_SER,
MEMORY_AND_DISK_SER_2, OFF_HEAP

Returns:

    

`Table` – Persisted table.

rename(_mapping_)[[source]](_modules/hail/table.html#Table.rename)

    

Rename fields of the table.

Examples

Rename C1 to col1 and C2 to col2:

    
    
    >>> table_result = table1.rename({'C1' : 'col1', 'C2' : 'col2'})
    

Parameters:

    

**mapping** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)"), [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Mapping from old field names to new field names.

Notes

Any field that does not appear as a key in mapping will not be renamed.

Returns:

    

`Table` – Table with renamed fields.

repartition(_n_ , _shuffle
=True_)[[source]](_modules/hail/table.html#Table.repartition)

    

Change the number of partitions.

Examples

Repartition to 500 partitions:

    
    
    >>> table_result = table1.repartition(500)
    

Notes

Check the current number of partitions with `n_partitions()`.

The data in a dataset is divided into chunks called partitions, which may be
stored together or across a network, so that each partition may be read and
processed in parallel by available cores. When a table with \\(M\\) rows is
first imported, each of the \\(k\\) partitions will contain about \\(M/k\\) of
the rows. Since each partition has some computational overhead, decreasing the
number of partitions can improve performance after significant filtering.
Since it’s recommended to have at least 2 - 4 partitions per core, increasing
the number of partitions can allow one to take advantage of more cores.
Partitions are a core concept of distributed computation in Spark, see [their
documentation](http://spark.apache.org/docs/latest/programming-
guide.html#resilient-distributed-datasets-rdds) for details.

When `shuffle=True`, Hail does a full shuffle of the data and creates equal
sized partitions. When `shuffle=False`, Hail combines existing partitions to
avoid a full shuffle. These algorithms correspond to the repartition and
coalesce commands in Spark, respectively. In particular, when `shuffle=False`,
`n_partitions` cannot exceed current number of partitions.

Parameters:

    

  * **n** (_int_) – Desired number of partitions.

  * **shuffle** (_bool_) – If `True`, use full shuffle to repartition.

Returns:

    

`Table` – Repartitioned table.

_property _row

    

Returns a struct expression of all row-indexed fields, including keys.

Examples

The data type of the row struct:

    
    
    >>> table1.row.dtype
    dtype('struct{ID: int32, HT: int32, SEX: str, X: int32, Z: int32, C1: int32, C2: int32, C3: int32}')
    

The number of row fields:

    
    
    >>> len(table1.row)
    8
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all row fields, including key
fields.

_property _row_value

    

Returns a struct expression including all non-key row-indexed fields.

Examples

The data type of the row struct:

    
    
    >>> table1.row_value.dtype
    dtype('struct{HT: int32, SEX: str, X: int32, Z: int32, C1: int32, C2: int32, C3: int32}')
    

The number of row fields:

    
    
    >>> len(table1.row_value)
    7
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all non-key row fields.

sample(_p_ , _seed =None_)[[source]](_modules/hail/table.html#Table.sample)

    

Downsample the table by keeping each row with probability `p`.

Examples

Downsample the table to approximately 1% of its rows.

    
    
    >>> table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    >>> small_table1 = table1.sample(0.75, seed=0)
    >>> small_table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    >>> small_table1 = table1.sample(0.25, seed=4)
    >>> small_table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Parameters:

    

  * **p** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Probability of keeping each row.

  * **seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Random seed.

Returns:

    

`Table` – Table with approximately `p * n_rows` rows.

select(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/table.html#Table.select)

    

Select existing fields or create new fields by name, dropping the rest.

Examples

Select a few old fields and compute a new one:

    
    
    >>> table_result = table1.select(table1.C1, Y=table1.Z - table1.X)
    

Notes

This method creates new row-indexed fields. If a created field shares its name
with a global field of the table, the method will fail.

Note

**Using select**

Select and its sibling methods (`Table.select_globals()`,
[`MatrixTable.select_globals()`](hail.MatrixTable.html#hail.MatrixTable.select_globals
"hail.MatrixTable.select_globals"),
[`MatrixTable.select_rows()`](hail.MatrixTable.html#hail.MatrixTable.select_rows
"hail.MatrixTable.select_rows"),
[`MatrixTable.select_cols()`](hail.MatrixTable.html#hail.MatrixTable.select_cols
"hail.MatrixTable.select_cols"), and
[`MatrixTable.select_entries()`](hail.MatrixTable.html#hail.MatrixTable.select_entries
"hail.MatrixTable.select_entries")) accept both variable-length (`f(x, y, z)`)
and keyword (`f(a=x, b=y, c=z)`) arguments.

Select methods will always preserve the key along that axis; e.g. for
`Table.select()`, the table key will aways be kept. To modify the key, use
`key_by()`.

Variable-length arguments can be either strings or expressions that reference
a (possibly nested) field of the table. Keyword arguments can be arbitrary
expressions.

**The following three usages are all equivalent** , producing a new table with
fields C1 and C2 of table1, and the table key ID.

First, variable-length string arguments:

    
    
    >>> table_result = table1.select('C1', 'C2')
    

Second, field reference variable-length arguments:

    
    
    >>> table_result = table1.select(table1.C1, table1.C2)
    

Last, expression keyword arguments:

    
    
    >>> table_result = table1.select(C1 = table1.C1, C2 = table1.C2)
    

Additionally, the variable-length argument syntax also permits nested field
references. Given the following struct field s:

    
    
    >>> table3 = table1.annotate(s = hl.struct(x=table1.X, z=table1.Z))
    

The following two usages are equivalent, producing a table with one field, x.:

    
    
    >>> table3_result = table3.select(table3.s.x)
    
    
    
    >>> table3_result = table3.select(x = table3.s.x)
    

The keyword argument syntax permits arbitrary expressions:

    
    
    >>> table_result = table1.select(foo=table1.X ** 2 + 1)
    

These syntaxes can be mixed together, with the stipulation that all keyword
arguments must come at the end due to Python language restrictions.

    
    
    >>> table_result = table1.select(table1.X, 'Z', bar = [table1.C1, table1.C2])
    

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`Table` – Table with specified fields.

select_globals(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/table.html#Table.select_globals)

    

Select existing global fields or create new fields by name, dropping the rest.

Examples

Selecting two global fields, one by name and one new one, replacing any
previously annotated global fields.

    
    
    >>> ht = hl.utils.range_table(1)
    >>> ht = ht.annotate_globals(pops = ['EUR', 'AFR', 'EAS', 'SAS'])
    >>> ht = ht.annotate_globals(study_name = 'HGDP+1kg')
    >>> ht.describe()
    ----------------------------------------
    Global fields:
        'pops': array<str>
        'study_name': str
    ----------------------------------------
    Row fields:
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> ht = ht.select_globals(ht.pops, target_date='2025-01-01')
    >>> ht.describe()
    ----------------------------------------
    Global fields:
        'pops': array<str>
        'target_date': str
    ----------------------------------------
    Row fields:
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    

Fields may also be selected by their name:

    
    
    >>> ht = ht.select_globals('target_date')
    >>> ht.globals.show()
    +--------------------+
    | <expr>.target_date |
    +--------------------+
    | str                |
    +--------------------+
    | "2025-01-01"       |
    +--------------------+
    

Notes

This method creates new global fields. If a created field shares its name with
a row-indexed field of the table, the method will fail.

Note

See `Table.select()` for more information about using `select` methods.

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`Table` – Table with specified global fields.

semi_join(_other_)[[source]](_modules/hail/table.html#Table.semi_join)

    

Filters the table to rows whose key appears in other.

Parameters:

    

**other** (`Table`) – Table with compatible key field(s).

Returns:

    

`Table`

Notes

The key type of the table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the table to keys present in another table.

To discard keys present in other, use `anti_join()`.

Examples

    
    
    >>> table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    >>> small_table2 = table2.head(2)
    >>> small_table2.show()
    +-------+-------+-------+
    |    ID |     A | B     |
    +-------+-------+-------+
    | int32 | int32 | str   |
    +-------+-------+-------+
    |     1 |    65 | "cat" |
    |     2 |    72 | "dog" |
    +-------+-------+-------+
    >>> table1.semi_join(small_table2).show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

It may be expensive to key the left-side table by the right-side key. In this
case, it is possible to implement a semi-join using a non-key field as
follows:

    
    
    >>> table1.filter(hl.is_defined(small_table2.index(table1['ID']))).show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

See also

`anti_join()`

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_)[[source]](_modules/hail/table.html#Table.show)

    

Print the first few rows of the table to the console.

Examples

Show the first lines of the table:

    
    
    >>> table1.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n or n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show, or negative to show all rows.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break fields.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

  * **handler** (_Callable[[str], Any]_) – Handler function for data string.

summarize(_handler
=None_)[[source]](_modules/hail/table.html#Table.summarize)

    

Compute and print summary information about the fields in the table.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

tail(_n_)[[source]](_modules/hail/table.html#Table.tail)

    

Subset table to last n rows.

Examples

Subset to the last three rows:

    
    
    >>> table_result = table1.tail(3)
    >>> table_result.count()
    3
    

Notes

The number of partitions in the new table is equal to the number of partitions
containing the last n rows.

Parameters:

    

**n** (_int_) – Number of rows to include.

Returns:

    

`Table` – Table including the last n rows.

take(_n_ , __localize =True_)[[source]](_modules/hail/table.html#Table.take)

    

Collect the first n rows of the table into a local list.

Examples

Take the first three rows:

    
    
    >>> first3 = table1.take(3)
    >>> first3
    [Struct(ID=1, HT=65, SEX='M', X=5, Z=4, C1=2, C2=50, C3=5),
     Struct(ID=2, HT=72, SEX='M', X=6, Z=3, C1=2, C2=61, C3=1),
     Struct(ID=3, HT=70, SEX='F', X=7, Z=3, C1=10, C2=81, C3=-5)]
    

Notes

This method does not need to look at all the data in the table, and allows for
fast queries of the start of the table.

This method is equivalent to `Table.head()` followed by `Table.collect()`.

Parameters:

    

**n** (_int_) – Number of rows to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of ```Struct```
– List of row structs.

to_matrix_table(_row_key_ , _col_key_ , _row_fields =[]_, _col_fields =[]_,
_n_partitions
=None_)[[source]](_modules/hail/table.html#Table.to_matrix_table)

    

Construct a matrix table from a table in coordinate representation.

Examples

Import a coordinate-representation table from disk:

    
    
    >>> coord_ht = hl.import_table('data/coordinate_matrix.tsv', impute=True)
    >>> coord_ht.show()
    +---------+---------+----------+
    | row_idx | col_idx |        x |
    +---------+---------+----------+
    |   int32 |   int32 |  float64 |
    +---------+---------+----------+
    |       1 |       1 | 2.50e-01 |
    |       1 |       2 | 3.30e-01 |
    |       2 |       1 | 1.10e-01 |
    |       3 |       1 | 1.00e+00 |
    |       3 |       2 | 0.00e+00 |
    +---------+---------+----------+
    

Convert to a matrix table and show:

    
    
    >>> dense_mt = coord_ht.to_matrix_table(row_key=['row_idx'], col_key=['col_idx'])
    >>> dense_mt.show()
    +---------+----------+----------+
    | row_idx |      1.x |      2.x |
    +---------+----------+----------+
    |   int32 |  float64 |  float64 |
    +---------+----------+----------+
    |       1 | 2.50e-01 | 3.30e-01 |
    |       2 | 1.10e-01 |       NA |
    |       3 | 1.00e+00 | 0.00e+00 |
    +---------+----------+----------+
    

Notes

Any row fields in the table that do not appear in one of the arguments to this
method are assumed to be entry fields of the resulting matrix table.

Parameters:

    

  * **row_key** (_Sequence[str]_) – Fields to be used as row key.

  * **col_key** (_Sequence[str]_) – Fields to be used as column key.

  * **row_fields** (_Sequence[str]_) – Fields to be stored once per row.

  * **col_fields** (_Sequence[str]_) – Fields to be stored once per column.

  * **n_partitions** (_int or None_) – Number of partitions.

Returns:

    

```MatrixTable```

to_matrix_table_row_major(_columns_ , _entry_field_name =None_,
_col_field_name
='col'_)[[source]](_modules/hail/table.html#Table.to_matrix_table_row_major)

    

Construct a matrix table from a table in row major representation. Each
element in columns is a field that will become an entry field in the matrix
table. Fields omitted from columns become row fields. If columns are structs,
then the matrix table will have the entry fields of those structs. Otherwise,
the matrix table will have one entry field named entry_field_name whose values
come from the values of the columns fields. The matrix table is column indexed
by col_field_name.

If you find yourself using this method after
[`import_table()`](methods/impex.html#hail.methods.import_table
"hail.methods.import_table"), consider instead using
[`import_matrix_table()`](methods/impex.html#hail.methods.import_matrix_table
"hail.methods.import_matrix_table").

Examples

Convert a table of RNA expression samples to a
```MatrixTable```:

    
    
    >>> t = hl.import_table('data/rna_expression.tsv', impute=True)
    >>> t = t.key_by('gene')
    >>> t.show()
    +---------+---------+---------+----------+-----------+-----------+-----------+
    | gene    | lung001 | lung002 | heart001 | muscle001 | muscle002 | muscle003 |
    +---------+---------+---------+----------+-----------+-----------+-----------+
    | str     |   int32 |   int32 |    int32 |     int32 |     int32 |     int32 |
    +---------+---------+---------+----------+-----------+-----------+-----------+
    | "LD4"   |       1 |       2 |        0 |         2 |         1 |         1 |
    | "SCN1A" |       2 |       1 |        1 |         0 |         0 |         0 |
    | "TITIN" |       3 |       0 |        0 |         1 |         2 |         1 |
    +---------+---------+---------+----------+-----------+-----------+-----------+
    >>> mt = t.to_matrix_table_row_major(
    ...          columns=['lung001', 'lung002', 'heart001',
    ...                   'muscle001', 'muscle002', 'muscle003'],
    ...          entry_field_name='expression',
    ...          col_field_name='sample')
    >>> mt.describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Column fields:
        'sample': str
    ----------------------------------------
    Row fields:
        'gene': str
    ----------------------------------------
    Entry fields:
        'expression': int32
    ----------------------------------------
    Column key: ['sample']
    Row key: ['gene']
    ----------------------------------------
    >>> mt.show(n_cols=2)
    +---------+----------------------+----------------------+
    | gene    | 'lung001'.expression | 'lung002'.expression |
    +---------+----------------------+----------------------+
    | str     |                int32 |                int32 |
    +---------+----------------------+----------------------+
    | "LD4"   |                    1 |                    2 |
    | "SCN1A" |                    2 |                    1 |
    | "TITIN" |                    3 |                    0 |
    +---------+----------------------+----------------------+
    showing the first 2 of 6 columns
    

Notes

All fields in columns must have the same type.

Parameters:

    

  * **columns** (_Sequence[str]_) – Fields to be used as columns.

  * **entry_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or None) – Field name for the entries of the matrix table.

  * **col_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Field name for the columns of the matrix table.

Returns:

    

```MatrixTable```

to_pandas(_flatten =True_, _types
={}_)[[source]](_modules/hail/table.html#Table.to_pandas)

    

Converts this table to a Pandas DataFrame.

Parameters:

    

  * **flatten** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If `True`, `flatten()` before converting to Pandas DataFrame.

  * **types** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") mapping [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```HailType``` to [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Dictionary defining Pandas DataFrame dtypes. If a key is [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)"), a field with the specified key name is mapped to a Pandas dtype. If a key is ```HailType```, all fields with the specified ```HailType``` are mapped to a Pandas dtype.

Returns:

    

[`pandas.DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame
"\(in pandas v2.3.0\)")

to_spark(_flatten =True_)[[source]](_modules/hail/table.html#Table.to_spark)

    

Converts this table to a Spark DataFrame.

Because Spark cannot represent complex types, types are expanded before
flattening or conversion.

Parameters:

    

**flatten** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool
"\(in Python v3.9\)")) – If `True`, `flatten()` before converting to Spark
DataFrame.

Returns:

    

[`pyspark.sql.DataFrame`](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrame.html#pyspark.sql.DataFrame
"\(in PySpark vmaster\)")

transmute(_**
named_exprs_)[[source]](_modules/hail/table.html#Table.transmute)

    

Add new fields and drop fields referenced.

Examples

Consider this table:

    
    
    >>> ht = table1
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Transmuting a field without referencing other fields has the same effect as
annotating:

    
    
    >>> ht = ht.transmute(new_field=hl.struct(x=3, y=4))
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    |    ID |    HT | SEX |     X |     Z |    C1 |    C2 |    C3 | new_field.x |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |       int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    |     1 |    65 | "M" |     5 |     4 |     2 |    50 |     5 |           3 |
    |     2 |    72 | "M" |     6 |     3 |     2 |    61 |     1 |           3 |
    |     3 |    70 | "F" |     7 |     3 |    10 |    81 |    -5 |           3 |
    |     4 |    60 | "F" |     8 |     2 |    11 |    90 |   -10 |           3 |
    +-------+-------+-----+-------+-------+-------+-------+-------+-------------+
    +-------------+
    | new_field.y |
    +-------------+
    |       int32 |
    +-------------+
    |           4 |
    |           4 |
    |           4 |
    |           4 |
    +-------------+
    

Transmuting a field while referencing other fields drops those other fields.
Notice how the compound field, new_field is dropped entirely even though we
only used one of its component fields.

    
    
    >>> ht = ht.transmute(F=ht.X + 2 * ht.new_field.x)
    >>> ht.show()
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |    ID |    HT | SEX |     Z |    C1 |    C2 |    C3 |     F |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    | int32 | int32 | str | int32 | int32 | int32 | int32 | int32 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    |     1 |    65 | "M" |     4 |     2 |    50 |     5 |    11 |
    |     2 |    72 | "M" |     3 |     2 |    61 |     1 |    12 |
    |     3 |    70 | "F" |     3 |    10 |    81 |    -5 |    13 |
    |     4 |    60 | "F" |     2 |    11 |    90 |   -10 |    14 |
    +-------+-------+-----+-------+-------+-------+-------+-------+
    

Notes

This method functions to create new row-indexed fields and consume fields
found in the expressions in named_exprs.

All row-indexed top-level fields found in an expression are dropped after the
new fields are created.

Note

`transmute()` will not drop key fields.

Warning

References to fields inside a top-level struct will remove the entire struct,
as field new_field was removed in the example above since new_field.x was
referenced.

Note

This method does not support aggregation.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – New field expressions.

Returns:

    

`Table` – Table with transmuted fields.

transmute_globals(_**
named_exprs_)[[source]](_modules/hail/table.html#Table.transmute_globals)

    

Similar to `Table.annotate_globals()`, but drops referenced fields.

Notes

Consider a table with global fields population, area, and year:

    
    
    >>> ht = hl.utils.range_table(1)
    >>> ht = ht.annotate_globals(population=1000000, area=500, year=2020)
    

Compute a new field, density from population and area and also drop the latter
two fields:

    
    
    >>> ht = ht.transmute_globals(density=ht.population / ht.area)
    >>> ht.globals.show()
    +-------------+----------------+
    | <expr>.year | <expr>.density |
    +-------------+----------------+
    |       int32 |        float64 |
    +-------------+----------------+
    |        2020 |       2.00e+03 |
    +-------------+----------------+
    

Introduce a new global field next_year based on year:

    
    
    >>> ht = ht.transmute_globals(next_year=ht.year + 1)
    >>> ht.globals.show()
    +----------------+------------------+
    | <expr>.density | <expr>.next_year |
    +----------------+------------------+
    |        float64 |            int32 |
    +----------------+------------------+
    |       2.00e+03 |             2021 |
    +----------------+------------------+
    

See also

`Table.transmute()`, `Table.select_globals()`, `Table.annotate_globals()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`Table`

union(_* tables_, _unify
=False_)[[source]](_modules/hail/table.html#Table.union)

    

Union the rows of multiple tables.

Examples

Take the union of rows from two tables:

    
    
    >>> union_table = table1.union(other_table)
    

Notes

If a row appears in more than one table identically, it is duplicated in the
result. All tables must have the same key names and types. They must also have
the same row types, unless the unify parameter is `True`, in which case a
field appearing in any table will be included in the result, with missing
values for tables that do not contain the field. If a field appears in
multiple tables with incompatible types, like arrays and strings, then an
error will be raised.

Parameters:

    

  * **tables** (varargs of `Table`) – Tables to union.

  * **unify** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Attempt to unify table field.

Returns:

    

`Table` – Table with all rows from each component table.

unpersist()[[source]](_modules/hail/table.html#Table.unpersist)

    

Unpersists this table from memory/disk.

Notes

This function will have no effect on a table that was not previously
persisted.

Returns:

    

`Table` – Unpersisted table.

write(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_)[[source]](_modules/hail/table.html#Table.write)

    

Write to disk.

Examples

    
    
    >>> table1.write('output/table1.ht', overwrite=True)
    

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

See also

[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table")

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`.

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

write_many(_output_ , _fields_ , _*_ , _overwrite =False_, _stage_locally
=False_, __codec_spec
=None_)[[source]](_modules/hail/table.html#Table.write_many)

    

Write fields to distinct tables.

Examples

    
    
    >>> t = hl.utils.range_table(10)
    >>> t = t.annotate(a = t.idx, b = t.idx * t.idx, c = hl.str(t.idx))
    >>> t.write_many('output-many', fields=('a', 'b', 'c'), overwrite=True)
    >>> hl.read_table('output-many/a').describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Row fields:
        'a': int32
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> hl.read_table('output-many/a').show()
    +-------+-------+
    |     a |   idx |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |     0 |
    |     1 |     1 |
    |     2 |     2 |
    |     3 |     3 |
    |     4 |     4 |
    |     5 |     5 |
    |     6 |     6 |
    |     7 |     7 |
    |     8 |     8 |
    |     9 |     9 |
    +-------+-------+
    >>> hl.read_table('output-many/b').describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Row fields:
        'b': int32
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> hl.read_table('output-many/b').show()
    +-------+-------+
    |     b |   idx |
    +-------+-------+
    | int32 | int32 |
    +-------+-------+
    |     0 |     0 |
    |     1 |     1 |
    |     4 |     2 |
    |     9 |     3 |
    |    16 |     4 |
    |    25 |     5 |
    |    36 |     6 |
    |    49 |     7 |
    |    64 |     8 |
    |    81 |     9 |
    +-------+-------+
    >>> hl.read_table('output-many/c').describe()
    ----------------------------------------
    Global fields:
        None
    ----------------------------------------
    Row fields:
        'c': str
        'idx': int32
    ----------------------------------------
    Key: ['idx']
    ----------------------------------------
    >>> hl.read_table('output-many/c').show()
    +-----+-------+
    | c   |   idx |
    +-----+-------+
    | str | int32 |
    +-----+-------+
    | "0" |     0 |
    | "1" |     1 |
    | "2" |     2 |
    | "3" |     3 |
    | "4" |     4 |
    | "5" |     5 |
    | "6" |     6 |
    | "7" |     7 |
    | "8" |     8 |
    | "9" |     9 |
    +-----+-------+
    

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

See also

[`read_table()`](methods/impex.html#hail.methods.read_table
"hail.methods.read_table")

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **fields** (_list of str_) – The fields to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`.

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

---

# GroupedTable

_class _hail.GroupedTable[[source]](_modules/hail/table.html#GroupedTable)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Table grouped by row that can be aggregated into a new table.

There are only two operations on a grouped table,
`GroupedTable.partition_hint()` and `GroupedTable.aggregate()`.

Attributes

Methods

`aggregate` | Aggregate by group, used after ```Table.group_by()```.  
---|---  
`partition_hint` | Set the target number of partitions for aggregation.  
  
aggregate(_**
named_exprs_)[[source]](_modules/hail/table.html#GroupedTable.aggregate)

    

Aggregate by group, used after
[`Table.group_by()`](hail.Table.html#hail.Table.group_by
"hail.Table.group_by").

Examples

Compute the mean value of X and the sum of Z per unique ID:

    
    
    >>> table_result = (table1.group_by(table1.ID)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    

Group by a height bin and compute sex ratio per bin:

    
    
    >>> table_result = (table1.group_by(height_bin = table1.HT // 20)
    ...                       .aggregate(fraction_female = hl.agg.fraction(table1.SEX == 'F')))
    

Notes

The resulting table has a key field for each group and a value field for each
aggregation. The names of the aggregation expressions must be distinct from
the names of the groups.

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

```Table``` – Aggregated table.

partition_hint(_n_)[[source]](_modules/hail/table.html#GroupedTable.partition_hint)

    

Set the target number of partitions for aggregation.

Examples

Use partition_hint in a
[`Table.group_by()`](hail.Table.html#hail.Table.group_by
"hail.Table.group_by") / `GroupedTable.aggregate()` pipeline:

    
    
    >>> table_result = (table1.group_by(table1.ID)
    ...                       .partition_hint(5)
    ...                       .aggregate(meanX = hl.agg.mean(table1.X), sumZ = hl.agg.sum(table1.Z)))
    

Notes

Until Hail’s query optimizer is intelligent enough to sample records at all
stages of a pipeline, it can be necessary in some places to provide some
explicit hints.

The default number of partitions for `GroupedTable.aggregate()` is the number
of partitions in the upstream table. If the aggregation greatly reduces the
size of the table, providing a hint for the target number of partitions can
accelerate downstream operations.

Parameters:

    

**n** (_int_) – Number of partitions.

Returns:

    

`GroupedTable` – Same grouped table with a partition hint.

---

# MatrixTable

_class
_hail.MatrixTable[[source]](_modules/hail/matrixtable.html#MatrixTable)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Hail’s distributed implementation of a structured matrix.

Use [`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table") to read a matrix table that was written with
`MatrixTable.write()`.

Examples

Add annotations:

    
    
    >>> dataset = dataset.annotate_globals(pli = {'SCN1A': 0.999, 'SONIC': 0.014},
    ...                                    populations = ['AFR', 'EAS', 'EUR', 'SAS', 'AMR', 'HIS'])
    
    
    
    >>> dataset = dataset.annotate_cols(pop = dataset.populations[hl.int(hl.rand_unif(0, 6))],
    ...                                 sample_gq = hl.agg.mean(dataset.GQ),
    ...                                 sample_dp = hl.agg.mean(dataset.DP))
    
    
    
    >>> dataset = dataset.annotate_rows(variant_gq = hl.agg.mean(dataset.GQ),
    ...                                 variant_dp = hl.agg.mean(dataset.GQ),
    ...                                 sas_hets = hl.agg.count_where(dataset.GT.is_het()))
    
    
    
    >>> dataset = dataset.annotate_entries(gq_by_dp = dataset.GQ / dataset.DP)
    

Filter:

    
    
    >>> dataset = dataset.filter_cols(dataset.pop != 'EUR')
    
    
    
    >>> datasetm = dataset.filter_rows((dataset.variant_gq > 10) & (dataset.variant_dp > 5))
    
    
    
    >>> dataset = dataset.filter_entries(dataset.gq_by_dp > 1)
    

Query:

    
    
    >>> col_stats = dataset.aggregate_cols(hl.struct(pop_counts=hl.agg.counter(dataset.pop),
    ...                                              high_quality=hl.agg.fraction((dataset.sample_gq > 10) & (dataset.sample_dp > 5))))
    >>> print(col_stats.pop_counts)
    >>> print(col_stats.high_quality)
    
    
    
    >>> het_dist = dataset.aggregate_rows(hl.agg.stats(dataset.sas_hets))
    >>> print(het_dist)
    
    
    
    >>> entry_stats = dataset.aggregate_entries(hl.struct(call_rate=hl.agg.fraction(hl.is_defined(dataset.GT)),
    ...                                                   global_gq_mean=hl.agg.mean(dataset.GQ)))
    >>> print(entry_stats.call_rate)
    >>> print(entry_stats.global_gq_mean)
    

Attributes

`col` | Returns a struct expression of all column-indexed fields, including keys.  
---|---  
`col_key` | Column key struct.  
`col_value` | Returns a struct expression including all non-key column-indexed fields.  
`entry` | Returns a struct expression including all row-and-column-indexed fields.  
`globals` | Returns a struct expression including all global fields.  
`row` | Returns a struct expression of all row-indexed fields, including keys.  
`row_key` | Row key struct.  
`row_value` | Returns a struct expression including all non-key row-indexed fields.  
  
Methods

`add_col_index` | Add the integer index of each column as a new column field.  
---|---  
`add_row_index` | Add the integer index of each row as a new row field.  
`aggregate_cols` | Aggregate over columns to a local value.  
`aggregate_entries` | Aggregate over entries to a local value.  
`aggregate_rows` | Aggregate over rows to a local value.  
`annotate_cols` | Create new column-indexed fields by name.  
`annotate_entries` | Create new row-and-column-indexed fields by name.  
`annotate_globals` | Create new global fields by name.  
`annotate_rows` | Create new row-indexed fields by name.  
`anti_join_cols` | Filters the table to columns whose key does not appear in other.  
`anti_join_rows` | Filters the table to rows whose key does not appear in other.  
`cache` | Persist the dataset in memory.  
`checkpoint` | Checkpoint the matrix table to disk by writing and reading using a fast, but less space-efficient codec.  
`choose_cols` | Choose a new set of columns from a list of old column indices.  
`collect_cols_by_key` | Collect values for each unique column key into arrays.  
`cols` | Returns a table with all column fields in the matrix.  
`compute_entry_filter_stats` | Compute statistics about the number and fraction of filtered entries.  
`count` | Count the number of rows and columns in the matrix.  
`count_cols` | Count the number of columns in the matrix.  
`count_rows` | Count the number of rows in the matrix.  
`describe` | Print information about the fields in the matrix table.  
`distinct_by_col` | Remove columns with a duplicate row key, keeping exactly one column for each unique key.  
`distinct_by_row` | Remove rows with a duplicate row key, keeping exactly one row for each unique key.  
`drop` | Drop fields.  
`entries` | Returns a matrix in coordinate table form.  
`explode_cols` | Explodes a column field of type array or set, copying the entire column for each element.  
`explode_rows` | Explodes a row field of type array or set, copying the entire row for each element.  
`filter_cols` | Filter columns of the matrix.  
`filter_entries` | Filter entries of the matrix.  
`filter_rows` | Filter rows of the matrix.  
`from_parts` | Create a MatrixTable from its component parts.  
`from_rows_table` | Construct matrix table with no columns from a table.  
`globals_table` | Returns a table with a single row with the globals of the matrix table.  
`group_cols_by` | Group columns, used with ```GroupedMatrixTable.aggregate()```.  
`group_rows_by` | Group rows, used with ```GroupedMatrixTable.aggregate()```.  
`head` | Subset matrix to first n_rows rows and n_cols cols.  
`index_cols` | Expose the column values as if looked up in a dictionary, indexing with exprs.  
`index_entries` | Expose the entries as if looked up in a dictionary, indexing with exprs.  
`index_globals` | Return this matrix table's global variables for use in another expression context.  
`index_rows` | Expose the row values as if looked up in a dictionary, indexing with exprs.  
`key_cols_by` | Key columns by a new set of fields.  
`key_rows_by` | Key rows by a new set of fields.  
`localize_entries` | Convert the matrix table to a table with entries localized as an array of structs.  
`make_table` | Make a table from a matrix table with one field per sample.  
`n_partitions` | Number of partitions.  
`naive_coalesce` | Naively decrease the number of partitions.  
`persist` | Persist this table in memory or on disk.  
`rename` | Rename fields of a matrix table.  
`repartition` | Change the number of partitions.  
`rows` | Returns a table with all row fields in the matrix.  
`sample_cols` | Downsample the matrix table by keeping each column with probability `p`.  
`sample_rows` | Downsample the matrix table by keeping each row with probability `p`.  
`select_cols` | Select existing column fields or create new fields by name, dropping the rest.  
`select_entries` | Select existing entry fields or create new fields by name, dropping the rest.  
`select_globals` | Select existing global fields or create new fields by name, dropping the rest.  
`select_rows` | Select existing row fields or create new fields by name, dropping all other non-key fields.  
`semi_join_cols` | Filters the matrix table to columns whose key appears in other.  
`semi_join_rows` | Filters the matrix table to rows whose key appears in other.  
`show` | Print the first few rows of the matrix table to the console.  
`summarize` | Compute and print summary information about the fields in the matrix table.  
`tail` | Subset matrix to last n rows.  
`transmute_cols` | Similar to `MatrixTable.annotate_cols()`, but drops referenced fields.  
`transmute_entries` | Similar to `MatrixTable.annotate_entries()`, but drops referenced fields.  
`transmute_globals` | Similar to `MatrixTable.annotate_globals()`, but drops referenced fields.  
`transmute_rows` | Similar to `MatrixTable.annotate_rows()`, but drops referenced fields.  
`unfilter_entries` | Unfilters filtered entries, populating fields with missing values.  
`union_cols` | Take the union of dataset columns.  
`union_rows` | Take the union of dataset rows.  
`unpersist` | Unpersists this dataset from memory/disk.  
`write` | Write to disk.  
  
add_col_index(_name
='col_idx'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.add_col_index)

    

Add the integer index of each column as a new column field.

Examples

    
    
    >>> dataset_result = dataset.add_col_index()
    

Notes

The field added is type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32").

The column index is 0-indexed; the values are found in the range `[0, N)`,
where `N` is the total number of columns.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Name for column index field.

Returns:

    

`MatrixTable` – Dataset with new field.

add_row_index(_name
='row_idx'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.add_row_index)

    

Add the integer index of each row as a new row field.

Examples

    
    
    >>> dataset_result = dataset.add_row_index()
    

Notes

The field added is type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64").

The row index is 0-indexed; the values are found in the range `[0, N)`, where
`N` is the total number of rows.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Name for row index field.

Returns:

    

`MatrixTable` – Dataset with new field.

aggregate_cols(_expr_ , __localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.aggregate_cols)

    

Aggregate over columns to a local value.

Examples

Aggregate over columns:

    
    
    >>> dataset.aggregate_cols(
    ...    hl.struct(fraction_female=hl.agg.fraction(dataset.pheno.is_female),
    ...              case_ratio=hl.agg.count_where(dataset.is_case) / hl.agg.count()))
    Struct(fraction_female=0.44, case_ratio=1.0)
    

Notes

Unlike most `MatrixTable` methods, this method does not support meaningful
references to fields that are not global or indexed by column.

This method should be thought of as a more convenient alternative to the
following:

    
    
    >>> cols_table = dataset.cols()
    >>> cols_table.aggregate(
    ...     hl.struct(fraction_female=hl.agg.fraction(cols_table.pheno.is_female),
    ...               case_ratio=hl.agg.count_where(cols_table.is_case) / hl.agg.count()))
    

Note

This method supports (and expects!) aggregation over columns.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expression.

Returns:

    

_any_ – Aggregated value dependent on expr.

aggregate_entries(_expr_ , __localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.aggregate_entries)

    

Aggregate over entries to a local value.

Examples

Aggregate over entries:

    
    
    >>> dataset.aggregate_entries(hl.struct(global_gq_mean=hl.agg.mean(dataset.GQ),
    ...                                     call_rate=hl.agg.fraction(hl.is_defined(dataset.GT))))
    Struct(global_gq_mean=69.60514541387025, call_rate=0.9933333333333333)
    

Notes

This method should be thought of as a more convenient alternative to the
following:

    
    
    >>> entries_table = dataset.entries()
    >>> entries_table.aggregate(hl.struct(global_gq_mean=hl.agg.mean(entries_table.GQ),
    ...                                   call_rate=hl.agg.fraction(hl.is_defined(entries_table.GT))))
    

Note

This method supports (and expects!) aggregation over entries.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

_any_ – Aggregated value dependent on expr.

aggregate_rows(_expr_ , __localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.aggregate_rows)

    

Aggregate over rows to a local value.

Examples

Aggregate over rows:

    
    
    >>> dataset.aggregate_rows(hl.struct(n_high_quality=hl.agg.count_where(dataset.qual > 40),
    ...                                  mean_qual=hl.agg.mean(dataset.qual)))
    Struct(n_high_quality=9, mean_qual=140054.73333333334)
    

Notes

Unlike most `MatrixTable` methods, this method does not support meaningful
references to fields that are not global or indexed by row.

This method should be thought of as a more convenient alternative to the
following:

    
    
    >>> rows_table = dataset.rows()
    >>> rows_table.aggregate(hl.struct(n_high_quality=hl.agg.count_where(rows_table.qual > 40),
    ...                                mean_qual=hl.agg.mean(rows_table.qual)))
    

Note

This method supports (and expects!) aggregation over rows.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expression.

Returns:

    

_any_ – Aggregated value dependent on expr.

annotate_cols(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_cols)

    

Create new column-indexed fields by name.

Examples

Compute statistics about the GQ distribution per sample:

    
    
    >>> dataset_result = dataset.annotate_cols(sample_gq_stats = hl.agg.stats(dataset.GQ))
    

Add sample metadata from a [`hail.Table`](hail.Table.html#hail.Table
"hail.Table").

    
    
    >>> dataset_result = dataset.annotate_cols(population = s_metadata[dataset.s].pop)
    

Note

This method supports aggregation over rows. For instance, the usage:

    
    
    >>> dataset_result = dataset.annotate_cols(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per column.

Notes

This method creates new column fields, but can also overwrite existing fields.
Only same-scope fields can be overwritten: for example, it is not possible to
annotate a global field foo and later create an column field foo. However, it
would be possible to create an column field foo and later create another
column field foo, overwriting the first.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new column-indexed field(s).

annotate_entries(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_entries)

    

Create new row-and-column-indexed fields by name.

Examples

Compute the allele dosage using the PL field:

    
    
    >>> def get_dosage(pl):
    ...    # convert to linear scale
    ...    linear_scaled = pl.map(lambda x: 10 ** - (x / 10))
    ...
    ...    # normalize to sum to 1
    ...    ls_sum = hl.sum(linear_scaled)
    ...    linear_scaled = linear_scaled.map(lambda x: x / ls_sum)
    ...
    ...    # multiply by [0, 1, 2] and sum
    ...    return hl.sum(linear_scaled * [0, 1, 2])
    >>>
    >>> dataset_result = dataset.annotate_entries(dosage = get_dosage(dataset.PL))
    

Note

This method does not support aggregation.

Notes

This method creates new entry fields, but can also overwrite existing fields.
Only same-scope fields can be overwritten: for example, it is not possible to
annotate a global field foo and later create an entry field foo. However, it
would be possible to create an entry field foo and later create another entry
field foo, overwriting the first.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new row-and-column-indexed field(s).

annotate_globals(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_globals)

    

Create new global fields by name.

Examples

Add two global fields:

    
    
    >>> pops_1kg = {'EUR', 'AFR', 'EAS', 'SAS', 'AMR'}
    >>> dataset_result = dataset.annotate_globals(pops_in_1kg = pops_1kg,
    ...                                           gene_list = ['SHH', 'SCN1A', 'SPTA1', 'DISC1'])
    

Add global fields from another table and matrix table:

    
    
    >>> dataset_result = dataset.annotate_globals(thing1 = dataset2.index_globals().global_field,
    ...                                           thing2 = v_metadata.index_globals().global_field)
    

Note

This method does not support aggregation.

Notes

This method creates new global fields, but can also overwrite existing fields.
Only same-scope fields can be overwritten: for example, it is not possible to
annotate a row field foo and later create an global field foo. However, it
would be possible to create an global field foo and later create another
global field foo, overwriting the first.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new global field(s).

annotate_rows(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.annotate_rows)

    

Create new row-indexed fields by name.

Examples

Compute call statistics for high quality samples per variant:

    
    
    >>> high_quality_calls = hl.agg.filter(dataset.sample_qc.gq_stats.mean > 20,
    ...                                    hl.agg.call_stats(dataset.GT, dataset.alleles))
    >>> dataset_result = dataset.annotate_rows(call_stats = high_quality_calls)
    

Add functional annotations from a [`Table`](hail.Table.html#hail.Table
"hail.Table"), v_metadata, and a `MatrixTable`, dataset2_AF, both keyed by
locus and alleles.

    
    
    >>> dataset_result = dataset.annotate_rows(consequence = v_metadata[dataset.locus, dataset.alleles].consequence,
    ...                                        dataset2_AF = dataset2.index_rows(dataset.row_key).info.AF)
    

Note

This method supports aggregation over columns. For instance, the usage:

    
    
    >>> dataset_result = dataset.annotate_rows(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per row.

Notes

This method creates new row fields, but can also overwrite existing fields.
Only non-key, same-scope fields can be overwritten: for example, it is not
possible to annotate a global field foo and later create an row field foo.
However, it would be possible to create an row field foo and later create
another row field foo, overwriting the first, as long as foo is not a row key.

The arguments to the method should either be
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") objects, or should be implicitly interpretable as
expressions.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – Matrix table with new row-indexed field(s).

anti_join_cols(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.anti_join_cols)

    

Filters the table to columns whose key does not appear in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The column key type of the matrix table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the matrix table to column keys not present in another table.

To restrict to columns whose key is present in other, use `semi_join_cols()`.

Examples

    
    
    >>> ds_result = ds.anti_join_cols(cols_to_remove)
    

It may be inconvenient to key the matrix table by the right-side key. In this
case, it is possible to implement an anti-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_cols(hl.is_missing(cols_to_remove.index(ds['s'])))
    

See also

`semi_join_cols()`, `filter_cols()`, `anti_join_rows()`

anti_join_rows(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.anti_join_rows)

    

Filters the table to rows whose key does not appear in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The row key type of the matrix table must match the key type of other.

This method does not change the schema of the table; it is a method of
filtering the matrix table to row keys not present in another table.

To restrict to rows whose key is present in other, use `semi_join_rows()`.

Examples

    
    
    >>> ds_result = ds.anti_join_rows(rows_to_remove)
    

It may be expensive to key the matrix table by the right-side key. In this
case, it is possible to implement an anti-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_rows(hl.is_missing(rows_to_remove.index(ds['locus'], ds['alleles'])))
    

See also

`anti_join_rows()`, `filter_rows()`, `anti_join_cols()`

cache()[[source]](_modules/hail/matrixtable.html#MatrixTable.cache)

    

Persist the dataset in memory.

Examples

Persist the dataset in memory:

    
    
    >>> dataset = dataset.cache() 
    

Notes

This method is an alias for `persist("MEMORY_ONLY")`.

Returns:

    

`MatrixTable` – Cached dataset.

checkpoint(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_, __read_if_exists =False_, __intervals =None_, __filter_intervals
=False_, __drop_cols =False_, __drop_rows
=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.checkpoint)

    

Checkpoint the matrix table to disk by writing and reading using a fast, but
less space-efficient codec.

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

Returns:

    

`MatrixTable`

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

Notes

An alias for `write()` followed by
[`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table"). It is possible to read the file at this
path later with
[`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table"). A faster, but less efficient, codec is used
or writing the data so the file will be larger than if one used `write()`.

Examples

    
    
    >>> dataset = dataset.checkpoint('output/dataset_checkpoint.mt')
    

choose_cols(_indices_)[[source]](_modules/hail/matrixtable.html#MatrixTable.choose_cols)

    

Choose a new set of columns from a list of old column indices.

Examples

Randomly shuffle column order:

    
    
    >>> import random
    >>> indices = list(range(dataset.count_cols()))
    >>> random.shuffle(indices)
    >>> dataset_reordered = dataset.choose_cols(indices)
    

Take the first ten columns:

    
    
    >>> dataset_result = dataset.choose_cols(list(range(10)))
    

Parameters:

    

**indices** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list
"\(in Python v3.9\)") of
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")) – List of old column indices.

Returns:

    

`MatrixTable`

_property _col

    

Returns a struct expression of all column-indexed fields, including keys.

Examples

Get all column field names:

    
    
    >>> list(dataset.col)  
    ['s', 'sample_qc', 'is_case', 'pheno', 'cov', 'cov1', 'cov2', 'cohorts', 'pop']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all column fields.

_property _col_key

    

Column key struct.

Examples

Get the column key field names:

    
    
    >>> list(dataset.col_key)
    ['s']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

_property _col_value

    

Returns a struct expression including all non-key column-indexed fields.

Examples

Get all non-key column field names:

    
    
    >>> list(dataset.col_value)  
    ['sample_qc', 'is_case', 'pheno', 'cov', 'cov1', 'cov2', 'cohorts', 'pop']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all column fields, minus keys.

collect_cols_by_key()[[source]](_modules/hail/matrixtable.html#MatrixTable.collect_cols_by_key)

    

Collect values for each unique column key into arrays.

Examples

    
    
    >>> mt = hl.utils.range_matrix_table(3, 3)
    >>> col_dict = hl.literal({0: [1], 1: [2, 3], 2: [4, 5, 6]})
    >>> mt = (mt.annotate_cols(foo = col_dict.get(mt.col_idx))
    ...     .explode_cols('foo'))
    >>> mt = mt.annotate_entries(bar = mt.row_idx * mt.foo)
    
    
    
    >>> mt.cols().show() 
    +---------+-------+
    | col_idx |   foo |
    +---------+-------+
    |   int32 | int32 |
    +---------+-------+
    |       0 |     1 |
    |       1 |     2 |
    |       1 |     3 |
    |       2 |     4 |
    |       2 |     5 |
    |       2 |     6 |
    +---------+-------+
    
    
    
    >>> mt.entries().show() 
    +---------+---------+-------+-------+
    | row_idx | col_idx |   foo |   bar |
    +---------+---------+-------+-------+
    |   int32 |   int32 | int32 | int32 |
    +---------+---------+-------+-------+
    |       0 |       0 |     1 |     0 |
    |       0 |       1 |     2 |     0 |
    |       0 |       1 |     3 |     0 |
    |       0 |       2 |     4 |     0 |
    |       0 |       2 |     5 |     0 |
    |       0 |       2 |     6 |     0 |
    |       1 |       0 |     1 |     1 |
    |       1 |       1 |     2 |     2 |
    |       1 |       1 |     3 |     3 |
    |       1 |       2 |     4 |     4 |
    +---------+---------+-------+-------+
    showing top 10 rows
    
    
    
    >>> mt = mt.collect_cols_by_key()
    >>> mt.cols().show()
    +---------+--------------+
    | col_idx | foo          |
    +---------+--------------+
    |   int32 | array<int32> |
    +---------+--------------+
    |       0 | [1]          |
    |       1 | [2,3]        |
    |       2 | [4,5,6]      |
    +---------+--------------+
    
    
    
    >>> mt.entries().show() 
    +---------+---------+--------------+--------------+
    | row_idx | col_idx | foo          | bar          |
    +---------+---------+--------------+--------------+
    |   int32 |   int32 | array<int32> | array<int32> |
    +---------+---------+--------------+--------------+
    |       0 |       0 | [1]          | [0]          |
    |       0 |       1 | [2,3]        | [0,0]        |
    |       0 |       2 | [4,5,6]      | [0,0,0]      |
    |       1 |       0 | [1]          | [1]          |
    |       1 |       1 | [2,3]        | [2,3]        |
    |       1 |       2 | [4,5,6]      | [4,5,6]      |
    |       2 |       0 | [1]          | [2]          |
    |       2 |       1 | [2,3]        | [4,6]        |
    |       2 |       2 | [4,5,6]      | [8,10,12]    |
    +---------+---------+--------------+--------------+
    

Notes

Each entry field and each non-key column field of type t is replaced by a
field of type array<t>. The value of each such field is an array containing
all values of that field sharing the corresponding column key. In each column,
the newly collected arrays all have the same length, and the values of each
pre-collection column are guaranteed to be located at the same index in their
corresponding arrays.

Note

The order of the columns is not guaranteed.

Returns:

    

`MatrixTable`

cols()[[source]](_modules/hail/matrixtable.html#MatrixTable.cols)

    

Returns a table with all column fields in the matrix.

Examples

Extract the column table:

    
    
    >>> cols_table = dataset.cols()
    

Warning

Matrix table columns are typically sorted by the order at import, and not
necessarily by column key. Since tables are always sorted by key, the table
which results from this command will have its rows sorted by the column key
(which becomes the table key). To preserve the original column order as the
table row order, first unkey the columns using `key_cols_by()` with no
arguments.

Returns:

    

```Table``` – Table with all column
fields from the matrix, with one row per column of the matrix.

compute_entry_filter_stats(_row_field ='entry_stats_row'_, _col_field
='entry_stats_col'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.compute_entry_filter_stats)

    

Compute statistics about the number and fraction of filtered entries.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Parameters:

    

  * **row_field** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name for computed row field (default: `entry_stats_row`.

  * **col_field** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name for computed column field (default: `entry_stats_col`.

Returns:

    

`MatrixTable`

Notes

Adds a new row field, row_field, and a new column field, col_field, each of
which are structs with the following fields:

>   * _n_filtered_ ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")) - Number of filtered entries per row or column.
>
>   * _n_remaining_ ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")) - Number of entries not filtered per row or
> column.
>
>   * _fraction_filtered_ ([`tfloat32`](types.html#hail.expr.types.tfloat32
> "hail.expr.types.tfloat32")) - Number of filtered entries divided by the
> total number of filtered and remaining entries.
>
>

See also

`filter_entries()`, `unfilter_entries()`

count()[[source]](_modules/hail/matrixtable.html#MatrixTable.count)

    

Count the number of rows and columns in the matrix.

Examples

    
    
    >>> dataset.count()
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)"), [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)") – Number of rows, number of cols.

count_cols(__localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.count_cols)

    

Count the number of columns in the matrix.

Examples

Count the number of columns:

    
    
    >>> n_cols = dataset.count_cols()
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Number of columns in the matrix.

count_rows(__localize
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.count_rows)

    

Count the number of rows in the matrix.

Examples

Count the number of rows:

    
    
    >>> n_rows = dataset.count_rows()
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Number of rows in the matrix.

describe(_handler= <built-in function print>_, _*_ ,
_widget=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.describe)

    

Print information about the fields in the matrix table.

Note

The widget argument is **experimental**.

Parameters:

    

  * **handler** (_Callable[[str], None]_) – Handler function for returned string.

  * **widget** (_bool_) – Create an interactive IPython widget.

distinct_by_col()[[source]](_modules/hail/matrixtable.html#MatrixTable.distinct_by_col)

    

Remove columns with a duplicate row key, keeping exactly one column for each
unique key.

Returns:

    

`MatrixTable`

distinct_by_row()[[source]](_modules/hail/matrixtable.html#MatrixTable.distinct_by_row)

    

Remove rows with a duplicate row key, keeping exactly one row for each unique
key.

Returns:

    

`MatrixTable`

drop(_* exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.drop)

    

Drop fields.

Examples

Drop fields PL (an entry field), info (a row field), and pheno (a column
field): using strings:

    
    
    >>> dataset_result = dataset.drop('PL', 'info', 'pheno')
    

Drop fields PL (an entry field), info (a row field), and pheno (a column
field): using field references:

    
    
    >>> dataset_result = dataset.drop(dataset.PL, dataset.info, dataset.pheno)
    

Drop a list of fields:

    
    
    >>> fields_to_drop = ['PL', 'info', 'pheno']
    >>> dataset_result = dataset.drop(*fields_to_drop)
    

Notes

This method can be used to drop global, row-indexed, column-indexed, or row-
and-column-indexed (entry) fields. The arguments can be either strings
(`'field'`), or top-level field references (`table.field` or
`table['field']`).

Key fields (belonging to either the row key or the column key) cannot be
dropped using this method. In order to drop a key field, use `key_rows_by()`
or `key_cols_by()` to remove the field from the key before dropping.

While many operations exist independently for rows, columns, entries, and
globals, only one is needed for dropping due to the lack of any necessary
contextual information.

Parameters:

    

**exprs** (varargs of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or [`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Names of fields to drop or field reference
expressions.

Returns:

    

`MatrixTable` – Matrix table without specified fields.

entries()[[source]](_modules/hail/matrixtable.html#MatrixTable.entries)

    

Returns a matrix in coordinate table form.

Examples

Extract the entry table:

    
    
    >>> entries_table = dataset.entries()
    

Notes

The coordinate table representation of the source matrix table contains one
row for each **non-filtered** entry of the matrix – if a matrix table has no
filtered entries and contains N rows and M columns, the table will contain `M
* N` rows, which can be **a very large number**.

This representation can be useful for aggregating over both axes of a matrix
table at the same time – it is not possible to aggregate over a matrix table
using `group_rows_by()` and `group_cols_by()` at the same time (aggregating by
population and chromosome from a variant-by-sample genetics representation,
for instance). After moving to the coordinate representation with `entries()`,
it is possible to group and aggregate the resulting table much more flexibly,
albeit with potentially poorer computational performance.

Warning

The table returned by this method should be used for aggregation or queries,
but never exported or written to disk without extensive filtering and field
selection – the disk footprint of an entries_table could be 100x (or more!)
larger than its parent matrix. This means that if you try to export the
entries table of a 10 terabyte matrix, you could write a petabyte of data!

Warning

Matrix table columns are typically sorted by the order at import, and not
necessarily by column key. Since tables are always sorted by key, the table
which results from this command will have its rows sorted by the compound (row
key, column key) which becomes the table key. To preserve the original row-
major entry order as the table row order, first unkey the columns using
`key_cols_by()` with no arguments.

Warning

If the matrix table has no row key, but has a column key, this operation may
require a full shuffle to sort by the column key, depending on the pipeline.

Returns:

    

```Table``` – Table with all non-global
fields from the matrix, with **one row per entry of the matrix**.

_property _entry

    

Returns a struct expression including all row-and-column-indexed fields.

Examples

Get all entry field names:

    
    
    >>> list(dataset.entry)
    ['GT', 'AD', 'DP', 'GQ', 'PL']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all entry fields.

explode_cols(_field_expr_)[[source]](_modules/hail/matrixtable.html#MatrixTable.explode_cols)

    

Explodes a column field of type array or set, copying the entire column for
each element.

Examples

Explode columns by annotated cohorts:

    
    
    >>> dataset_result = dataset.explode_cols(dataset.cohorts)
    

Notes

The new matrix table will have N copies of each column, where N is the number
of elements that column contains for the field denoted by field_expr. The
field referenced in field_expr is replaced in the sequence of duplicated
columns by the sequence of elements in the array or set. All other fields
remain the same, including entry fields.

If the field referenced with field_expr is missing or empty, the column is
removed entirely.

Parameters:

    

**field_expr** (str or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field name or (possibly nested) field reference
expression.

Returns:

    

`MatrixTable` – Matrix table exploded column-wise for each element of
field_expr.

explode_rows(_field_expr_)[[source]](_modules/hail/matrixtable.html#MatrixTable.explode_rows)

    

Explodes a row field of type array or set, copying the entire row for each
element.

Examples

Explode rows by annotated genes:

    
    
    >>> dataset_result = dataset.explode_rows(dataset.gene)
    

Notes

The new matrix table will have N copies of each row, where N is the number of
elements that row contains for the field denoted by field_expr. The field
referenced in field_expr is replaced in the sequence of duplicated rows by the
sequence of elements in the array or set. All other fields remain the same,
including entry fields.

If the field referenced with field_expr is missing or empty, the row is
removed entirely.

Parameters:

    

**field_expr** (str or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Field name or (possibly nested) field reference
expression.

Returns:

    

`MatrixTable` – Matrix table exploded row-wise for each element of field_expr.

filter_cols(_expr_ , _keep
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.filter_cols)

    

Filter columns of the matrix.

Examples

Keep columns where pheno.is_case is `True` and pheno.age is larger than 50:

    
    
    >>> dataset_result = dataset.filter_cols(dataset.pheno.is_case &
    ...                                      (dataset.pheno.age > 50),
    ...                                      keep=True)
    

Remove columns where sample_qc.gq_stats.mean is less than 20:

    
    
    >>> dataset_result = dataset.filter_cols(dataset.sample_qc.gq_stats.mean < 20,
    ...                                      keep=False)
    

Remove columns where s is found in a Python set:

    
    
    >>> samples_to_remove = {'NA12878', 'NA12891', 'NA12892'}
    >>> set_to_remove = hl.literal(samples_to_remove)
    >>> dataset_result = dataset.filter_cols(~set_to_remove.contains(dataset['s']))
    

Notes

The expression expr will be evaluated for every column of the table. If keep
is `True`, then columns where expr evaluates to `True` will be kept (the
filter removes the columns where the predicate evaluates to `False`). If keep
is `False`, then columns where expr evaluates to `True` will be removed (the
filter keeps the columns where the predicate evaluates to `False`).

Warning

When expr evaluates to missing, the column will be removed regardless of keep.

Note

This method supports aggregation over rows. For instance,

    
    
    >>> dataset_result = dataset.filter_cols(hl.agg.mean(dataset.GQ) > 20.0)
    

will remove columns where the mean GQ of all entries in the column is smaller
than 20.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep columns where expr is true.

Returns:

    

`MatrixTable` – Filtered matrix table.

filter_entries(_expr_ , _keep
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.filter_entries)

    

Filter entries of the matrix.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep entries where expr is true.

Returns:

    

`MatrixTable` – Filtered matrix table.

Examples

Keep entries where the sum of AD is greater than 10 and GQ is greater than 20:

    
    
    >>> dataset_result = dataset.filter_entries((hl.sum(dataset.AD) > 10) & (dataset.GQ > 20))
    

Warning

When expr evaluates to missing, the entry will be removed regardless of keep.

Note

This method does not support aggregation.

Notes

The expression expr will be evaluated for every entry of the table. If keep is
`True`, then entries where expr evaluates to `True` will be kept (the filter
removes the entries where the predicate evaluates to `False`). If keep is
`False`, then entries where expr evaluates to `True` will be removed (the
filter keeps the entries where the predicate evaluates to `False`).

Filtered entries are removed entirely from downstream operations. This means
that the resulting matrix table has sparsity – that is, that the number of
entries is **smaller** than the product of `count_rows()` and `count_cols()`.
To re-densify a filtered matrix table, use the `unfilter_entries()` method to
restore filtered entries, populated all fields with missing values. Below are
some properties of an entry-filtered matrix table.

  1. Filtered entries are not included in the `entries()` table.

    
    
    >>> mt_range = hl.utils.range_matrix_table(10, 10)
    >>> mt_range = mt_range.annotate_entries(x = mt_range.row_idx + mt_range.col_idx)
    >>> mt_range.count()
    (10, 10)
    
    
    
    >>> mt_range.entries().count()
    100
    
    
    
    >>> mt_filt = mt_range.filter_entries(mt_range.x % 2 == 0)
    >>> mt_filt.count()
    (10, 10)
    
    
    
    >>> mt_filt.count_rows() * mt_filt.count_cols()
    100
    
    
    
    >>> mt_filt.entries().count()
    50
    

  2. Filtered entries are not included in aggregation.

    
    
    >>> mt_filt.aggregate_entries(hl.agg.count())
    50
    
    
    
    >>> mt_filt = mt_filt.annotate_cols(col_n = hl.agg.count())
    >>> mt_filt.col_n.take(5)
    [5, 5, 5, 5, 5]
    
    
    
    >>> mt_filt = mt_filt.annotate_rows(row_n = hl.agg.count())
    >>> mt_filt.row_n.take(5)
    [5, 5, 5, 5, 5]
    

  3. Annotating a new entry field will not annotate filtered entries.

    
    
    >>> mt_filt = mt_filt.annotate_entries(y = 1)
    >>> mt_filt.aggregate_entries(hl.agg.sum(mt_filt.y))
    50
    

4\. If all the entries in a row or column of a matrix table are filtered, the
row or column remains.

    
    
    >>> mt_filt.filter_entries(False).count()
    (10, 10)
    

See also

`unfilter_entries()`, `compute_entry_filter_stats()`

filter_rows(_expr_ , _keep
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.filter_rows)

    

Filter rows of the matrix.

Examples

Keep rows where variant_qc.AF is below 1%:

    
    
    >>> dataset_result = dataset.filter_rows(dataset.variant_qc.AF[1] < 0.01, keep=True)
    

Remove rows where filters is non-empty:

    
    
    >>> dataset_result = dataset.filter_rows(dataset.filters.size() > 0, keep=False)
    

Notes

The expression expr will be evaluated for every row of the table. If keep is
`True`, then rows where expr evaluates to `True` will be kept (the filter
removes the rows where the predicate evaluates to `False`). If keep is
`False`, then rows where expr evaluates to `True` will be removed (the filter
keeps the rows where the predicate evaluates to `False`).

Warning

When expr evaluates to missing, the row will be removed regardless of keep.

Note

This method supports aggregation over columns. For instance,

    
    
    >>> dataset_result = dataset.filter_rows(hl.agg.mean(dataset.GQ) > 20.0)
    

will remove rows where the mean GQ of all entries in the row is smaller than
20.

Parameters:

    

  * **expr** (bool or ```BooleanExpression```) – Filter expression.

  * **keep** (_bool_) – Keep rows where expr is true.

Returns:

    

`MatrixTable` – Filtered matrix table.

_static _from_parts(_globals =None_, _rows =None_, _cols =None_, _entries
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.from_parts)

    

Create a MatrixTable from its component parts.

Example

    
    
    >>> mt = hl.MatrixTable.from_parts(
    ...     globals={'hello':'world'},
    ...     rows={'foo':[1, 2]},
    ...     cols={'bar':[3, 4]},
    ...     entries={'baz':[[1, 2],[3, 4]]}
    ... )
    >>> mt.describe()
    ----------------------------------------
    Global fields:
        'hello': str
    ----------------------------------------
    Column fields:
        'col_idx': int32
        'bar': int32
    ----------------------------------------
    Row fields:
        'row_idx': int32
        'foo': int32
    ----------------------------------------
    Entry fields:
        'baz': int32
    ----------------------------------------
    Column key: ['col_idx']
    Row key: ['row_idx']
    ----------------------------------------
    >>> mt.row.show()
    +---------+-------+
    | row_idx |   foo |
    +---------+-------+
    |   int32 | int32 |
    +---------+-------+
    |       0 |     1 |
    |       1 |     2 |
    +---------+-------+
    >>> mt.col.show()
    +---------+-------+
    | col_idx |   bar |
    +---------+-------+
    |   int32 | int32 |
    +---------+-------+
    |       0 |     3 |
    |       1 |     4 |
    +---------+-------+
    >>> mt.entry.show()
    +---------+-------+-------+
    | row_idx | 0.baz | 1.baz |
    +---------+-------+-------+
    |   int32 | int32 | int32 |
    +---------+-------+-------+
    |       0 |     1 |     2 |
    |       1 |     3 |     4 |
    +---------+-------+-------+
    

Notes

  * Matrix dimensions are inferred from input data.

  * You must provide row and column dimensions by specifying rows or entries (inclusive) and cols or entries (inclusive).

  * The respective dimensions of rows, cols and entries must match should you provide rows and entries or cols and entries (inclusive).

Parameters:

    

  * **globals** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Global fields by name.

  * **rows** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Row fields by name.

  * **cols** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Column fields by name.

  * **entries** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") from [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`any`](https://docs.python.org/3.9/library/functions.html#any "\(in Python v3.9\)")) – Matrix entries by name in the form entry[row_idx][col_idx].

Returns:

    

`MatrixTable` – A MatrixTable assembled from inputs whose rows are keyed by
row_idx and columns are keyed by col_idx.

_classmethod
_from_rows_table(_table_)[[source]](_modules/hail/matrixtable.html#MatrixTable.from_rows_table)

    

Construct matrix table with no columns from a table.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Examples

Import a text table and construct a rows-only matrix table:

    
    
    >>> table = hl.import_table('data/variant-lof.tsv')
    >>> table = table.transmute(**hl.parse_variant(table['v'])).key_by('locus', 'alleles')
    >>> sites_mt = hl.MatrixTable.from_rows_table(table)
    

Notes

All fields in the table become row-indexed fields in the result.

Parameters:

    

**table** (```Table```) – The table to
be converted.

Returns:

    

`MatrixTable`

_property _globals

    

Returns a struct expression including all global fields.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

globals_table()[[source]](_modules/hail/matrixtable.html#MatrixTable.globals_table)

    

Returns a table with a single row with the globals of the matrix table.

Examples

Extract the globals table:

    
    
    >>> globals_table = dataset.globals_table()
    

Returns:

    

```Table``` – Table with the globals
from the matrix, with a single row.

group_cols_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.group_cols_by)

    

Group columns, used with
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

Examples

Aggregate to a matrix with cohort as column keys, computing the call rate as
an entry field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate(call_rate = hl.agg.fraction(hl.is_defined(dataset.GT))))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Column fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Column-indexed expressions to group by.

Returns:

    

[`GroupedMatrixTable`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable
"hail.GroupedMatrixTable") – Grouped matrix, can be used to call
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

group_rows_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.group_rows_by)

    

Group rows, used with
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Row fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Row-indexed expressions to group by.

Returns:

    

[`GroupedMatrixTable`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable
"hail.GroupedMatrixTable") – Grouped matrix. Can be used to call
[`GroupedMatrixTable.aggregate()`](hail.GroupedMatrixTable.html#hail.GroupedMatrixTable.aggregate
"hail.GroupedMatrixTable.aggregate").

head(_n_rows_ , _n_cols =None_, _*_ , _n
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.head)

    

Subset matrix to first n_rows rows and n_cols cols.

Examples

    
    
    >>> mt_range = hl.utils.range_matrix_table(100, 100)
    

Passing only one argument will take the first n_rows rows:

    
    
    >>> mt_range.head(10).count()
    (10, 100)
    

Passing two arguments refers to rows and columns, respectively:

    
    
    >>> mt_range.head(10, 20).count()
    (10, 20)
    

Either argument may be `None` to indicate no filter.

First 10 rows, all columns:

    
    
    >>> mt_range.head(10, None).count()
    (10, 100)
    

All rows, first 10 columns:

    
    
    >>> mt_range.head(None, 10).count()
    (100, 10)
    

Notes

The number of partitions in the new matrix is equal to the number of
partitions containing the first n_rows rows.

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of rows to include (all rows included if `None`).

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Number of cols to include (all cols included if `None`).

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Deprecated in favor of n_rows.

Returns:

    

`MatrixTable` – Matrix including the first n_rows rows and first n_cols cols.

index_cols(_* exprs_, _all_matches
=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.index_cols)

    

Expose the column values as if looked up in a dictionary, indexing with exprs.

Examples

    
    
    >>> dataset_result = dataset.annotate_cols(pheno = dataset2.index_cols(dataset.s).pheno)
    

Or equivalently:

    
    
    >>> dataset_result = dataset.annotate_cols(pheno = dataset2.index_cols(dataset.col_key).pheno)
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Index expressions.

  * **all_matches** (_bool_) – Experimental. If `True`, value of expression is array of all matches.

Notes

`index_cols(cols)` is equivalent to `cols().index(exprs)` or `cols()[exprs]`.

The type of the resulting struct is the same as the type of `col_value()`.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

index_entries(_row_exprs_ ,
_col_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.index_entries)

    

Expose the entries as if looked up in a dictionary, indexing with exprs.

Examples

    
    
    >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2.index_entries(dataset.row_key, dataset.col_key).GQ)
    

Or equivalently:

    
    
    >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2[dataset.row_key, dataset.col_key].GQ)
    

Parameters:

    

  * **row_exprs** (tuple of ```Expression```) – Row index expressions.

  * **col_exprs** (tuple of ```Expression```) – Column index expressions.

Notes

The type of the resulting struct is the same as the type of `entry()`.

Note

There is a shorthand syntax for `MatrixTable.index_entries()` using square
brackets (the Python `__getitem__` syntax). This syntax is preferred.

    
    
    >>> dataset_result = dataset.annotate_entries(GQ2 = dataset2[dataset.row_key, dataset.col_key].GQ)
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

index_globals()[[source]](_modules/hail/matrixtable.html#MatrixTable.index_globals)

    

Return this matrix table’s global variables for use in another expression
context.

Examples

    
    
    >>> dataset1 = dataset.annotate_globals(pli={'SCN1A': 0.999, 'SONIC': 0.014})
    >>> pli_dict = dataset1.index_globals().pli
    >>> dataset_result = dataset2.annotate_rows(gene_pli = dataset2.gene.map(lambda x: pli_dict.get(x)))
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

index_rows(_* exprs_, _all_matches
=False_)[[source]](_modules/hail/matrixtable.html#MatrixTable.index_rows)

    

Expose the row values as if looked up in a dictionary, indexing with exprs.

Examples

    
    
    >>> dataset_result = dataset.annotate_rows(qual = dataset2.index_rows(dataset.locus, dataset.alleles).qual)
    

Or equivalently:

    
    
    >>> dataset_result = dataset.annotate_rows(qual = dataset2.index_rows(dataset.row_key).qual)
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Index expressions.

  * **all_matches** (_bool_) – Experimental. If `True`, value of expression is array of all matches.

Notes

`index_rows(exprs)` is equivalent to `rows().index(exprs)` or `rows()[exprs]`.

The type of the resulting struct is the same as the type of `row_value()`.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

key_cols_by(_* keys_, _**
named_keys_)[[source]](_modules/hail/matrixtable.html#MatrixTable.key_cols_by)

    

Key columns by a new set of fields.

See ```Table.key_by()```
for more information on defining a key.

Parameters:

    

  * **keys** (varargs of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```.) – Column fields to key by.

  * **named_keys** (keyword args of ```Expression```.) – Column fields to key by.

Returns:

    

`MatrixTable`

key_rows_by(_* keys_, _**
named_keys_)[[source]](_modules/hail/matrixtable.html#MatrixTable.key_rows_by)

    

Key rows by a new set of fields.

Examples

    
    
    >>> dataset_result = dataset.key_rows_by('locus')
    >>> dataset_result = dataset.key_rows_by(dataset['locus'])
    >>> dataset_result = dataset.key_rows_by(**dataset.row_key.drop('alleles'))
    

All of these expressions key the dataset by the ‘locus’ field, dropping the
‘alleles’ field from the row key.

    
    
    >>> dataset_result = dataset.key_rows_by(contig=dataset['locus'].contig,
    ...                                      position=dataset['locus'].position,
    ...                                      alleles=dataset['alleles'])
    

This keys the dataset by the newly defined fields, ‘contig’ and ‘position’,
and the ‘alleles’ field. The old row key field, ‘locus’, is preserved as a
non-key field.

Notes

See ```Table.key_by()```
for more information on defining a key.

Parameters:

    

  * **keys** (varargs of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```.) – Row fields to key by.

  * **named_keys** (keyword args of ```Expression```.) – Row fields to key by.

Returns:

    

`MatrixTable`

localize_entries(_entries_array_field_name =None_, _columns_array_field_name
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.localize_entries)

    

Convert the matrix table to a table with entries localized as an array of
structs.

Examples

Build a numpy ndarray from a small `MatrixTable`:

    
    
    >>> mt = hl.utils.range_matrix_table(3,3)
    >>> mt = mt.select_entries(x = mt.row_idx * mt.col_idx)
    >>> mt.show()
    +---------+-------+-------+-------+
    | row_idx |   0.x |   1.x |   2.x |
    +---------+-------+-------+-------+
    |   int32 | int32 | int32 | int32 |
    +---------+-------+-------+-------+
    |       0 |     0 |     0 |     0 |
    |       1 |     0 |     1 |     2 |
    |       2 |     0 |     2 |     4 |
    +---------+-------+-------+-------+
    
    
    
    >>> t = mt.localize_entries('entry_structs', 'columns')
    >>> t.describe()
    ----------------------------------------
    Global fields:
        'columns': array<struct {
            col_idx: int32
        }>
    ----------------------------------------
    Row fields:
        'row_idx': int32
        'entry_structs': array<struct {
            x: int32
        }>
    ----------------------------------------
    Key: ['row_idx']
    ----------------------------------------
    
    
    
    >>> t = t.select(entries = t.entry_structs.map(lambda entry: entry.x))
    >>> import numpy as np
    >>> np.array(t.entries.collect())
    array([[0, 0, 0],
           [0, 1, 2],
           [0, 2, 4]])
    

Notes

Both of the added fields are arrays of length equal to `mt.count_cols()`.
Missing entries are represented as missing structs in the entries array.

Parameters:

    

  * **entries_array_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the table field containing the array of entry structs for the given row.

  * **columns_array_field_name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The name of the global field containing the array of column structs.

Returns:

    

```Table``` – A table whose fields are
the row fields of this matrix table plus one field named
`entries_array_field_name`. The global fields of this table are the global
fields of this matrix table plus one field named `columns_array_field_name`.

make_table(_separator
='.'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.make_table)

    

Make a table from a matrix table with one field per sample.

Deprecated since version 0.2.129: use `localize_entries()` instead because it
supports more columns

Parameters:

    

**separator** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Separator between sample IDs and entry field names.

Returns:

    

```Table```

See also

`localize_entries()`

Notes

The table has one row for each row of the input matrix. The per sample and
entry fields are formed by concatenating the sample ID with the entry field
name using separator. If the entry field name is empty, the separator is
omitted.

The table inherits the globals from the matrix table.

Examples

Consider a matrix table with the following schema:

    
    
    Global fields:
        'batch': str
    Column fields:
        's': str
    Row fields:
        'locus': locus<GRCh37>
        'alleles': array<str>
    Entry fields:
        'GT': call
        'GQ': int32
    Column key:
        's': str
    Row key:
        'locus': locus<GRCh37>
        'alleles': array<str>
    

and three sample IDs: A, B and C. Then the result of `make_table()`:

    
    
    >>> ht = mt.make_table() 
    

has the original row fields along with 6 additional fields, one for each
sample and entry field:

    
    
    Global fields:
        'batch': str
    Row fields:
        'locus': locus<GRCh37>
        'alleles': array<str>
        'A.GT': call
        'A.GQ': int32
        'B.GT': call
        'B.GQ': int32
        'C.GT': call
        'C.GQ': int32
    Key:
        'locus': locus<GRCh37>
        'alleles': array<str>
    

n_partitions()[[source]](_modules/hail/matrixtable.html#MatrixTable.n_partitions)

    

Number of partitions.

Notes

The data in a dataset is divided into chunks called partitions, which may be
stored together or across a network, so that each partition may be read and
processed in parallel by available cores. Partitions are a core concept of
distributed computation in Spark, see
[here](http://spark.apache.org/docs/latest/programming-guide.html#resilient-
distributed-datasets-rdds) for details.

Returns:

    

_int_ – Number of partitions.

naive_coalesce(_max_partitions_)[[source]](_modules/hail/matrixtable.html#MatrixTable.naive_coalesce)

    

Naively decrease the number of partitions.

Example

Naively repartition to 10 partitions:

    
    
    >>> dataset_result = dataset.naive_coalesce(10)
    

Warning

`naive_coalesce()` simply combines adjacent partitions to achieve the desired
number. It does not attempt to rebalance, unlike `repartition()`, so it can
produce a heavily unbalanced dataset. An unbalanced dataset can be inefficient
to operate on because the work is not evenly distributed across partitions.

Parameters:

    

**max_partitions** (_int_) – Desired number of partitions. If the current
number of partitions is less than or equal to max_partitions, do nothing.

Returns:

    

`MatrixTable` – Matrix table with at most max_partitions partitions.

persist(_storage_level
='MEMORY_AND_DISK'_)[[source]](_modules/hail/matrixtable.html#MatrixTable.persist)

    

Persist this table in memory or on disk.

Examples

Persist the dataset to both memory and disk:

    
    
    >>> dataset = dataset.persist() 
    

Notes

The `MatrixTable.persist()` and `MatrixTable.cache()` methods store the
current dataset on disk or in memory temporarily to avoid redundant
computation and improve the performance of Hail pipelines. This method is not
a substitution for [`Table.write()`](hail.Table.html#hail.Table.write
"hail.Table.write"), which stores a permanent file.

Most users should use the “MEMORY_AND_DISK” storage level. See the [Spark
documentation](http://spark.apache.org/docs/latest/programming-guide.html#rdd-
persistence) for a more in-depth discussion of persisting data.

Parameters:

    

**storage_level** (_str_) – Storage level. One of: NONE, DISK_ONLY,
DISK_ONLY_2, MEMORY_ONLY, MEMORY_ONLY_2, MEMORY_ONLY_SER, MEMORY_ONLY_SER_2,
MEMORY_AND_DISK, MEMORY_AND_DISK_2, MEMORY_AND_DISK_SER,
MEMORY_AND_DISK_SER_2, OFF_HEAP

Returns:

    

`MatrixTable` – Persisted dataset.

rename(_fields_)[[source]](_modules/hail/matrixtable.html#MatrixTable.rename)

    

Rename fields of a matrix table.

Examples

Rename column key s to SampleID, still keying by SampleID.

    
    
    >>> dataset_result = dataset.rename({'s': 'SampleID'})
    

You can rename a field to a field name that already exists, as long as that
field also gets renamed (no name collisions). Here, we rename the column key s
to info, and the row field info to vcf_info:

    
    
    >>> dataset_result = dataset.rename({'s': 'info', 'info': 'vcf_info'})
    

Parameters:

    

**fields** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)") from
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") to [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Mapping from old field names to new field names.

Returns:

    

`MatrixTable` – Matrix table with renamed fields.

repartition(_n_partitions_ , _shuffle
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.repartition)

    

Change the number of partitions.

Examples

Repartition to 500 partitions:

    
    
    >>> dataset_result = dataset.repartition(500)
    

Notes

Check the current number of partitions with `n_partitions()`.

The data in a dataset is divided into chunks called partitions, which may be
stored together or across a network, so that each partition may be read and
processed in parallel by available cores. When a matrix with \\(M\\) rows is
first imported, each of the \\(k\\) partitions will contain about \\(M/k\\) of
the rows. Since each partition has some computational overhead, decreasing the
number of partitions can improve performance after significant filtering.
Since it’s recommended to have at least 2 - 4 partitions per core, increasing
the number of partitions can allow one to take advantage of more cores.
Partitions are a core concept of distributed computation in Spark, see [their
documentation](http://spark.apache.org/docs/latest/programming-
guide.html#resilient-distributed-datasets-rdds) for details.

When `shuffle=True`, Hail does a full shuffle of the data and creates equal
sized partitions. When `shuffle=False`, Hail combines existing partitions to
avoid a full shuffle. These algorithms correspond to the repartition and
coalesce commands in Spark, respectively. In particular, when `shuffle=False`,
`n_partitions` cannot exceed current number of partitions.

Parameters:

    

  * **n_partitions** (_int_) – Desired number of partitions.

  * **shuffle** (_bool_) – If `True`, use full shuffle to repartition.

Returns:

    

`MatrixTable` – Repartitioned dataset.

_property _row

    

Returns a struct expression of all row-indexed fields, including keys.

Examples

Get the first five row field names:

    
    
    >>> list(dataset.row)[:5]
    ['locus', 'alleles', 'rsid', 'qual', 'filters']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all row fields.

_property _row_key

    

Row key struct.

Examples

Get the row key field names:

    
    
    >>> list(dataset.row_key)
    ['locus', 'alleles']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression")

_property _row_value

    

Returns a struct expression including all non-key row-indexed fields.

Examples

Get the first five non-key row field names:

    
    
    >>> list(dataset.row_value)[:5]
    ['rsid', 'qual', 'filters', 'info', 'use_as_marker']
    

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of all row fields, minus keys.

rows()[[source]](_modules/hail/matrixtable.html#MatrixTable.rows)

    

Returns a table with all row fields in the matrix.

Examples

Extract the row table:

    
    
    >>> rows_table = dataset.rows()
    

Returns:

    

```Table``` – Table with all row fields
from the matrix, with one row per row of the matrix.

sample_cols(_p_ , _seed
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.sample_cols)

    

Downsample the matrix table by keeping each column with probability `p`.

Examples

Downsample the dataset to approximately 1% of its columns.

    
    
    >>> small_dataset = dataset.sample_cols(0.01)
    

Parameters:

    

  * **p** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Probability of keeping each column.

  * **seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Random seed.

Returns:

    

`MatrixTable` – Matrix table with approximately `p * n_cols` column.

sample_rows(_p_ , _seed
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.sample_rows)

    

Downsample the matrix table by keeping each row with probability `p`.

Examples

Downsample the dataset to approximately 1% of its rows.

    
    
    >>> small_dataset = dataset.sample_rows(0.01)
    

Notes

Although the `MatrixTable` returned by this method may be small, it requires a
full pass over the rows of the sampled object.

Parameters:

    

  * **p** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Probability of keeping each row.

  * **seed** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Random seed.

Returns:

    

`MatrixTable` – Matrix table with approximately `p * n_rows` rows.

select_cols(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_cols)

    

Select existing column fields or create new fields by name, dropping the rest.

Examples

Select existing fields and compute a new one:

    
    
    >>> dataset_result = dataset.select_cols(
    ...     dataset.sample_qc,
    ...     dataset.pheno.age,
    ...     isCohort1 = dataset.pheno.cohort_name == 'Cohort1')
    

Notes

This method creates new column fields. If a created field shares its name with
a differently-indexed field of the table, the method will fail.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method supports aggregation over rows. For instance, the usage:

    
    
    >>> dataset_result = dataset.select_cols(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per column.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified column fields.

select_entries(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_entries)

    

Select existing entry fields or create new fields by name, dropping the rest.

Examples

Drop all entry fields aside from GT:

    
    
    >>> dataset_result = dataset.select_entries(dataset.GT)
    

Notes

This method creates new entry fields. If a created field shares its name with
a differently-indexed field of the table, the method will fail.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified entry fields.

select_globals(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_globals)

    

Select existing global fields or create new fields by name, dropping the rest.

Examples

Select one existing field and compute a new one:

    
    
    >>> dataset_result = dataset.select_globals(dataset.global_field_1,
    ...                                         another_global=['AFR', 'EUR', 'EAS', 'AMR', 'SAS'])
    

Notes

This method creates new global fields. If a created field shares its name with
a differently-indexed field of the table, the method will fail.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method does not support aggregation.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified global fields.

select_rows(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.select_rows)

    

Select existing row fields or create new fields by name, dropping all other
non-key fields.

Examples

Select existing fields and compute a new one:

    
    
    >>> dataset_result = dataset.select_rows(
    ...    dataset.variant_qc.gq_stats.mean,
    ...    high_quality_cases = hl.agg.count_where((dataset.GQ > 20) &
    ...                                         dataset.is_case))
    

Notes

This method creates new row fields. If a created field shares its name with a
differently-indexed field of the table, or with a row key, the method will
fail.

Row keys are preserved. To drop or change a row key field, use
`MatrixTable.key_rows_by()`.

Note

See ```Table.select()```
for more information about using `select` methods.

Note

This method supports aggregation over columns. For instance, the usage:

    
    
    >>> dataset_result = dataset.select_rows(mean_GQ = hl.agg.mean(dataset.GQ))
    

will compute the mean per row.

Parameters:

    

  * **exprs** (variable-length args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Arguments that specify field names or nested field reference expressions.

  * **named_exprs** (keyword args of ```Expression```) – Field names and the expressions to compute them.

Returns:

    

`MatrixTable` – MatrixTable with specified row fields.

semi_join_cols(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.semi_join_cols)

    

Filters the matrix table to columns whose key appears in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The column key type of the matrix table must match the key type of other.

This method does not change the schema of the matrix table; it is a filtering
the matrix table to column keys not present in another table.

To discard collumns whose key is present in other, use `anti_join_cols()`.

Examples

    
    
    >>> ds_result = ds.semi_join_cols(cols_to_keep)
    

It may be inconvenient to key the matrix table by the right-side key. In this
case, it is possible to implement a semi-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_cols(hl.is_defined(cols_to_keep.index(ds['s'])))
    

See also

`anti_join_cols()`, `filter_cols()`, `semi_join_rows()`

semi_join_rows(_other_)[[source]](_modules/hail/matrixtable.html#MatrixTable.semi_join_rows)

    

Filters the matrix table to rows whose key appears in other.

Parameters:

    

**other** (```Table```) – Table with
compatible key field(s).

Returns:

    

`MatrixTable`

Notes

The row key type of the matrix table must match the key type of other.

This method does not change the schema of the matrix table; it is filtering
the matrix table to row keys present in another table.

To discard rows whose key is present in other, use `anti_join_rows()`.

Examples

    
    
    >>> ds_result = ds.semi_join_rows(rows_to_keep)
    

It may be expensive to key the matrix table by the right-side key. In this
case, it is possible to implement a semi-join using a non-key field as
follows:

    
    
    >>> ds_result = ds.filter_rows(hl.is_defined(rows_to_keep.index(ds['locus'], ds['alleles'])))
    

See also

`anti_join_rows()`, `filter_rows()`, `semi_join_cols()`

show(_n_rows =None_, _n_cols =None_, _include_row_fields =False_, _width
=None_, _truncate =None_, _types =True_, _handler
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.show)

    

Print the first few rows of the matrix table to the console.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> mt.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of columns to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break fields.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

  * **handler** (_Callable[[str], Any]_) – Handler function for data string.

summarize(_*_ , _rows =True_, _cols =True_, _entries =True_, _handler
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.summarize)

    

Compute and print summary information about the fields in the matrix table.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

Parameters:

    

  * **rows** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Compute summary for the row fields.

  * **cols** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Compute summary for the column fields.

  * **entries** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Compute summary for the entry fields.

tail(_n_rows_ , _n_cols =None_, _*_ , _n
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.tail)

    

Subset matrix to last n rows.

Examples

    
    
    >>> mt_range = hl.utils.range_matrix_table(100, 100)
    

Passing only one argument will take the last n rows:

    
    
    >>> mt_range.tail(10).count()
    (10, 100)
    

Passing two arguments refers to rows and columns, respectively:

    
    
    >>> mt_range.tail(10, 20).count()
    (10, 20)
    

Either argument may be `None` to indicate no filter.

Last 10 rows, all columns:

    
    
    >>> mt_range.tail(10, None).count()
    (10, 100)
    

All rows, last 10 columns:

    
    
    >>> mt_range.tail(None, 10).count()
    (100, 10)
    

Notes

For backwards compatibility, the n parameter is not named n_rows, but the
parameter refers to the number of rows to keep.

The number of partitions in the new matrix is equal to the number of
partitions containing the last n rows.

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of rows to include (all rows included if `None`).

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Number of cols to include (all cols included if `None`).

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Deprecated in favor of n_rows.

Returns:

    

`MatrixTable` – Matrix including the last n rows and last n_cols cols.

transmute_cols(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_cols)

    

Similar to `MatrixTable.annotate_cols()`, but drops referenced fields.

Notes

This method adds new column fields according to named_exprs, and drops all
column fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

Note

`transmute_cols()` will not drop key fields.

Note

This method supports aggregation over rows.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_cols()`,
`MatrixTable.annotate_cols()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

transmute_entries(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_entries)

    

Similar to `MatrixTable.annotate_entries()`, but drops referenced fields.

Notes

This method adds new entry fields according to named_exprs, and drops all
entry fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_entries()`,
`MatrixTable.annotate_entries()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

transmute_globals(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_globals)

    

Similar to `MatrixTable.annotate_globals()`, but drops referenced fields.

Notes

This method adds new global fields according to named_exprs, and drops all
global fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_globals()`,
`MatrixTable.annotate_globals()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

transmute_rows(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#MatrixTable.transmute_rows)

    

Similar to `MatrixTable.annotate_rows()`, but drops referenced fields.

Notes

This method adds new row fields according to named_exprs, and drops all row
fields referenced in those expressions. See
[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute") for full documentation on how transmute methods work.

Note

`transmute_rows()` will not drop key fields.

Note

This method supports aggregation over columns.

See also

[`Table.transmute()`](hail.Table.html#hail.Table.transmute
"hail.Table.transmute"), `MatrixTable.select_rows()`,
`MatrixTable.annotate_rows()`

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Annotation expressions.

Returns:

    

`MatrixTable`

unfilter_entries()[[source]](_modules/hail/matrixtable.html#MatrixTable.unfilter_entries)

    

Unfilters filtered entries, populating fields with missing values.

Returns:

    

`MatrixTable`

Notes

This method is used in the case that a pipeline downstream of
`filter_entries()` requires a fully dense (no filtered entries) matrix table.

Generally, if this method is required in a pipeline, the upstream pipeline can
be rewritten to use annotation instead of entry filtering.

See also

`filter_entries()`, `compute_entry_filter_stats()`

union_cols(_other_ , _row_join_type ='inner'_, _drop_right_row_fields
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.union_cols)

    

Take the union of dataset columns.

Warning

This method does not preserve the global fields from the other matrix table.

Examples

Union the columns of two datasets:

    
    
    >>> dataset_result = dataset_to_union_1.union_cols(dataset_to_union_2)
    

Notes

In order to combine two datasets, three requirements must be met:

>   * The row keys must match.
>
>   * The column key schemas and column schemas must match.
>
>   * The entry schemas must match.
>
>

The row fields in the resulting dataset are the row fields from the first
dataset; the row schemas do not need to match.

This method creates a `MatrixTable` which contains all columns from both input
datasets. The set of rows included in the result is determined by the
row_join_type parameter.

  * With the default value of `'inner'`, an inner join is performed on rows, so that only rows whose row key exists in both input datasets are included. In this case, the entries for each row are the concatenation of all entries of the corresponding rows in the input datasets.

  * With row_join_type set to `'outer'`, an outer join is perfomed on rows, so that row keys which exist in only one input dataset are also included. For those rows, the entry fields for the columns coming from the other dataset will be missing.

Only distinct row keys from each dataset are included (equivalent to calling
`distinct_by_row()` on each dataset first).

This method does not deduplicate; if a column key exists identically in two
datasets, then it will be duplicated in the result.

Parameters:

    

  * **other** (`MatrixTable`) – Dataset to concatenate.

  * **row_join_type** (```str```) – If outer, perform an outer join on rows; if ‘inner’, perform an inner join. Default inner.

  * **drop_right_row_fields** (```bool```) – If true, non-key row fields of other are dropped. Otherwise, non-key row fields in the two datasets must have distinct names, and the result contains the union of the row fields.

Returns:

    

`MatrixTable` – Dataset with columns from both datasets.

union_rows(_*_ , __check_cols
=True_)[[source]](_modules/hail/matrixtable.html#MatrixTable.union_rows)

    

Take the union of dataset rows.

Examples

Union the rows of two datasets:

    
    
    >>> dataset_result = dataset_to_union_1.union_rows(dataset_to_union_2)
    

Given a list of datasets, take the union of all rows:

    
    
    >>> all_datasets = [dataset_to_union_1, dataset_to_union_2]
    

The following three syntaxes are equivalent:

    
    
    >>> dataset_result = dataset_to_union_1.union_rows(dataset_to_union_2)
    >>> dataset_result = all_datasets[0].union_rows(*all_datasets[1:])
    >>> dataset_result = hl.MatrixTable.union_rows(*all_datasets)
    

Notes

In order to combine two datasets, three requirements must be met:

>   * The column keys must be identical, both in type, value, and ordering.
>
>   * The row key schemas and row schemas must match.
>
>   * The entry schemas must match.
>
>

The column fields in the resulting dataset are the column fields from the
first dataset; the column schemas do not need to match.

This method does not deduplicate; if a row exists identically in two datasets,
then it will be duplicated in the result.

Warning

This method can trigger a shuffle, if partitions from two datasets overlap.

Parameters:

    

**datasets** (varargs of `MatrixTable`) – Datasets to combine.

Returns:

    

`MatrixTable` – Dataset with rows from each member of datasets.

unpersist()[[source]](_modules/hail/matrixtable.html#MatrixTable.unpersist)

    

Unpersists this dataset from memory/disk.

Notes

This function will have no effect on a dataset that was not previously
persisted.

Returns:

    

`MatrixTable` – Unpersisted dataset.

write(_output_ , _overwrite =False_, _stage_locally =False_, __codec_spec
=None_, __partitions
=None_)[[source]](_modules/hail/matrixtable.html#MatrixTable.write)

    

Write to disk.

Examples

    
    
    >>> dataset.write('output/dataset.mt')
    

Danger

Do not write or checkpoint to a path that is already an input source for the
query. This can cause data loss.

See also

[`read_matrix_table()`](methods/impex.html#hail.methods.read_matrix_table
"hail.methods.read_matrix_table")

Parameters:

    

  * **output** (_str_) – Path at which to write.

  * **stage_locally** (_bool_) – If `True`, major output will be written to temporary local storage before being copied to `output`

  * **overwrite** (_bool_) – If `True`, overwrite an existing file at the destination.

---

# GroupedMatrixTable

_class
_hail.GroupedMatrixTable[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable)

    

Bases: [`BaseTable`](hail.table.BaseTable.html#hail.table.BaseTable
"hail.table.BaseTable")

Matrix table grouped by row or column that can be aggregated into a new matrix
table.

Attributes

Methods

`aggregate` | Aggregate entries by group, used after ```MatrixTable.group_rows_by()``` or ```MatrixTable.group_cols_by()```.  
---|---  
`aggregate_cols` | Aggregate cols by group.  
`aggregate_entries` | Aggregate entries by group.  
`aggregate_rows` | Aggregate rows by group.  
`describe` | Print information about grouped matrix table.  
`group_cols_by` | Group columns.  
`group_rows_by` | Group rows.  
`partition_hint` | Set the target number of partitions for aggregation.  
`result` | Return the result of aggregating by group.  
  
aggregate(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate)

    

Aggregate entries by group, used after
[`MatrixTable.group_rows_by()`](hail.MatrixTable.html#hail.MatrixTable.group_rows_by
"hail.MatrixTable.group_rows_by") or
[`MatrixTable.group_cols_by()`](hail.MatrixTable.html#hail.MatrixTable.group_cols_by
"hail.MatrixTable.group_cols_by").

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

Alias for `aggregate_entries()`, `result()`.

See also

`aggregate_entries()`, `result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

```MatrixTable``` –
Aggregated matrix table.

aggregate_cols(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate_cols)

    

Aggregate cols by group.

Examples

Aggregate to a matrix with cohort as column keys, computing the mean height
per cohort as a new column field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate_cols(mean_height = hl.agg.mean(dataset.pheno.height))
    ...                          .result())
    

Notes

The aggregation scope includes all column fields and global fields.

See also

`result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

`GroupedMatrixTable`

aggregate_entries(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate_entries)

    

Aggregate entries by group.

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
    ...                          .result())
    

See also

`aggregate()`, `result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

`GroupedMatrixTable`

aggregate_rows(_**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.aggregate_rows)

    

Aggregate rows by group.

Examples

Aggregate to a matrix with genes as row keys, collecting the functional
consequences per gene as a set as a new row field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate_rows(consequences = hl.agg.collect_as_set(dataset.consequence))
    ...                          .result())
    

Notes

The aggregation scope includes all row fields and global fields.

See also

`result()`

Parameters:

    

**named_exprs** (varargs of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Aggregation expressions.

Returns:

    

`GroupedMatrixTable`

describe(_handler= <built-in function
print>_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.describe)

    

Print information about grouped matrix table.

group_cols_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.group_cols_by)

    

Group columns.

Examples

Aggregate to a matrix with cohort as column keys, computing the call rate as
an entry field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate(call_rate = hl.agg.fraction(hl.is_defined(dataset.GT))))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Column fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Column-indexed expressions to group by.

Returns:

    

`GroupedMatrixTable` – Grouped matrix, can be used to call
`GroupedMatrixTable.aggregate()`.

group_rows_by(_* exprs_, _**
named_exprs_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.group_rows_by)

    

Group rows.

Examples

Aggregate to a matrix with genes as row keys, computing the number of non-
reference calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

All complex expressions must be passed as named expressions.

Parameters:

    

  * **exprs** (args of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```Expression```) – Row fields to group by.

  * **named_exprs** (keyword args of ```Expression```) – Row-indexed expressions to group by.

Returns:

    

`GroupedMatrixTable` – Grouped matrix. Can be used to call
`GroupedMatrixTable.aggregate()`.

partition_hint(_n_)[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.partition_hint)

    

Set the target number of partitions for aggregation.

Examples

Use partition_hint in a
[`MatrixTable.group_rows_by()`](hail.MatrixTable.html#hail.MatrixTable.group_rows_by
"hail.MatrixTable.group_rows_by") / `GroupedMatrixTable.aggregate()` pipeline:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .partition_hint(5)
    ...                          .aggregate(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref())))
    

Notes

Until Hail’s query optimizer is intelligent enough to sample records at all
stages of a pipeline, it can be necessary in some places to provide some
explicit hints.

The default number of partitions for `GroupedMatrixTable.aggregate()` is the
number of partitions in the upstream dataset. If the aggregation greatly
reduces the size of the dataset, providing a hint for the target number of
partitions can accelerate downstream operations.

Parameters:

    

**n** (_int_) – Number of partitions.

Returns:

    

`GroupedMatrixTable` – Same grouped matrix table with a partition hint.

result()[[source]](_modules/hail/matrixtable.html#GroupedMatrixTable.result)

    

Return the result of aggregating by group.

Examples

Aggregate to a matrix with genes as row keys, collecting the functional
consequences per gene as a row field and computing the number of non-reference
calls as an entry field:

    
    
    >>> dataset_result = (dataset.group_rows_by(dataset.gene)
    ...                          .aggregate_rows(consequences = hl.agg.collect_as_set(dataset.consequence))
    ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
    ...                          .result())
    

Aggregate to a matrix with cohort as column keys, computing the mean height
per cohort as a column field and computing the number of non-reference calls
as an entry field:

    
    
    >>> dataset_result = (dataset.group_cols_by(dataset.cohort)
    ...                          .aggregate_cols(mean_height = hl.agg.stats(dataset.pheno.height).mean)
    ...                          .aggregate_entries(n_non_ref = hl.agg.count_where(dataset.GT.is_non_ref()))
    ...                          .result())
    

See also

`aggregate()`

Returns:

    

```MatrixTable``` –
Aggregated matrix table.

---

# Expressions

`eval`(expression) | Evaluate a Hail expression, returning the result.  
---|---  
```Expression``` | Base class for Hail expressions.  
---|---  
```ArrayExpression``` | Expression of type ```tarray```.  
```ArrayNumericExpression``` | Expression of type ```tarray``` with a numeric type.  
```BooleanExpression``` | Expression of type ```tbool```.  
```CallExpression``` | Expression of type ```tcall```.  
```CollectionExpression``` | Expression of type ```tarray``` or ```tset```  
```DictExpression``` | Expression of type ```tdict```.  
```IntervalExpression``` | Expression of type ```tinterval```.  
```LocusExpression``` | Expression of type ```tlocus```.  
```NumericExpression``` | Expression of numeric type.  
```Int32Expression``` | Expression of type ```tint32```.  
```Int64Expression``` | Expression of type ```tint64```.  
```Float32Expression``` | Expression of type ```tfloat32```.  
```Float64Expression``` | Expression of type ```tfloat64```.  
```SetExpression``` | Expression of type ```tset```.  
```StringExpression``` | Expression of type ```tstr```.  
```StructExpression``` | Expression of type ```tstruct```.  
```TupleExpression``` | Expression of type ```ttuple```.  
```NDArrayExpression``` | Expression of type ```tndarray```.  
```NDArrayNumericExpression``` | Expression of type ```tndarray``` with a numeric element type.  
  
hail.expr.eval(_expression_)[[source]](_modules/hail/expr/expressions/expression_utils.html#eval)

    

Evaluate a Hail expression, returning the result.

This method is extremely useful for learning about Hail expressions and
understanding how to compose them.

The expression must have no indices, but can refer to the globals of a
```Table``` or
```MatrixTable```.

Examples

Evaluate a conditional:

    
    
    >>> x = 6
    >>> hl.eval(hl.if_else(x % 2 == 0, 'Even', 'Odd'))
    'Even'
    

Parameters:

    

**expression** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Any expression, or a Python value that can be
implicitly interpreted as an expression.

Returns:

    

_Any_

---

# Types

Fields and expressions in Hail have types. Throughout the documentation, you
will find type descriptions like `array<str>` or `tlocus`. It is generally
more important to know how to use expressions of various types than to know
how to manipulate the types themselves, but some operations like
[`missing()`](functions/core.html#hail.expr.functions.missing
"hail.expr.functions.missing") require type arguments.

In Python, `5` is of type
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") while `"hello"` is of type
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)"). Python is a dynamically-typed language, meaning that a function
like:

    
    
    >>> def add_x_and_y(x, y):
    ...     return x + y
    

…can be called on any two objects which can be added, like numbers, strings,
or [`numpy`](https://numpy.org/doc/stable/reference/index.html#module-numpy
"\(in NumPy v2.3\)") arrays.

Types are very important in Hail, because the fields of
```Table``` and
```MatrixTable```
objects have data types.

## Primitive types

Hail’s primitive data types for boolean, numeric and string objects are:

`tint` | Alias for `tint32`.  
---|---  
`tint32` | Hail type for signed 32-bit integers.  
`tint64` | Hail type for signed 64-bit integers.  
`tfloat` | Alias for `tfloat64`.  
`tfloat32` | Hail type for 32-bit floating point numbers.  
`tfloat64` | Hail type for 64-bit floating point numbers.  
`tstr` | Hail type for text strings.  
`tbool` | Hail type for Boolean (`True` or `False`) values.  
  
## Container types

Hail’s container types are:

>   * `tarray` \- Ordered collection of homogenous objects.
>
>   * `tndarray` \- Ordered n-dimensional arrays of homogenous objects.
>
>   * `tset` \- Unordered collection of distinct homogenous objects.
>
>   * `tdict` \- Key-value map. Keys and values are both homogenous.
>
>   * `ttuple` \- Tuple of heterogeneous values.
>
>   * `tstruct` \- Structure containing named fields, each with its own type.
>
>

`tarray` | Hail type for variable-length arrays of elements.  
---|---  
`tndarray` | Hail type for n-dimensional arrays.  
`tset` | Hail type for collections of distinct elements.  
`tdict` | Hail type for key-value maps.  
`ttuple` | Hail type for tuples.  
`tinterval` | Hail type for intervals of ordered values.  
`tstruct` | Hail type for structured groups of heterogeneous fields.  
  
## Genetics types

Hail has two genetics-specific types:

`tlocus` | Hail type for a genomic coordinate with a contig and a position.  
---|---  
`tcall` | Hail type for a diploid genotype.  
  
## When to work with types

In general, you won’t need to mention types explicitly.

There are a few situations where you may want to specify types explicitly:

  * To specify column types in ```import_table()``` if the impute flag does not infer the type you want.

  * When converting a Python value to a Hail expression with ```literal()```, if you don’t wish to rely on the inferred type.

  * With functions like ```missing()``` and ```empty_array()```.

## Viewing an object’s type

Hail objects have a `dtype` field that will print their type.

    
    
    >>> hl.rand_norm().dtype
    dtype('float64')
    

Printing the representation of a Hail expression will also show the type:

    
    
    >>> hl.rand_norm()
    <Float64Expression of type float64>
    

We can see that `hl.rand_norm()` is of type `tfloat64`, but what does
Expression mean? Each data type in Hail is represented by its own Expression
class. Data of type `tfloat64` is represented by an
[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression"). Data of type `tstruct` is represented by a
[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression").

## Collection Types

Hail’s collection types (arrays, ndarrays, sets, and dicts) have homogenous
elements, meaning that all values in the collection must be of the same type.
Python allows mixed collections: `['1', 2, 3.0]` is a valid Python list.
However, Hail arrays cannot contain both `tstr` and `tint32` values. Likewise,
the [`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in
Python v3.9\)") `{'a': 1, 2: 'b'}` is a valid Python dictionary, but a Hail
dictionary cannot contain keys of different types. An example of a valid
dictionary in Hail is `{'a': 1, 'b': 2}`, where the keys are all strings and
the values are all integers. The type of this dictionary would be `dict<str,
int32>`.

## Constructing types

Constructing types can be done either by using the type objects and classes
(prefixed by “t”) or by parsing from strings with `dtype()`. As an example, we
will construct a `tstruct` with each option:

    
    
    >>> t = hl.tstruct(a = hl.tint32, b = hl.tstr, c = hl.tarray(hl.tfloat64))
    >>> t
    dtype('struct{a: int32, b: str, c: array<float64>}')
    
    >>> t = hl.dtype('struct{a: int32, b: str, c: array<float64>}')
    >>> t
    dtype('struct{a: int32, b: str, c: array<float64>}')
    

## Reference documentation

_class
_hail.expr.types.HailType[[source]](_modules/hail/expr/types.html#HailType)

    

Hail type superclass.

hail.expr.types.dtype(_type_str_)[[source]](_modules/hail/expr/types.html#dtype)

    

Parse a type from its string representation.

Examples

    
    
    >>> hl.dtype('int')
    dtype('int32')
    
    
    
    >>> hl.dtype('float')
    dtype('float64')
    
    
    
    >>> hl.dtype('array<int32>')
    dtype('array<int32>')
    
    
    
    >>> hl.dtype('dict<str, bool>')
    dtype('dict<str, bool>')
    
    
    
    >>> hl.dtype('struct{a: int32, `field with spaces`: int64}')
    dtype('struct{a: int32, `field with spaces`: int64}')
    

Notes

This function is able to reverse `str(t)` on a `HailType`.

The grammar is defined as follows:

    
    
    type = _ ( array / bool / call / dict / interval / int64 / int32 / float32 / float64 / locus / ndarray / rng_state / set / stream / struct / str / tuple / union / void / variable ) _
    variable = "?" simple_identifier (":" simple_identifier)?
    void = "void" / "tvoid"
    int64 = "int64" / "tint64"
    int32 = "int32" / "tint32" / "int" / "tint"
    float32 = "float32" / "tfloat32"
    float64 = "float64" / "tfloat64" / "tfloat" / "float"
    bool = "tbool" / "bool"
    call = "tcall" / "call"
    str = "tstr" / "str"
    locus = ("tlocus" / "locus") _ "<" identifier ">"
    array = ("tarray" / "array") _ "<" type ">"
    ndarray = ("tndarray" / "ndarray") _ "<" type "," nat ">"
    set = ("tset" / "set") _ "<" type ">"
    stream = ("tstream" / "stream") _ "<" type ">"
    dict = ("tdict" / "dict") _ "<" type "," type ">"
    struct = ("tstruct" / "struct") _ "{" (fields / _) "}"
    union = ("tunion" / "union") _ "{" (fields / _) "}"
    tuple = ("ttuple" / "tuple") _ "(" ((type ("," type)*) / _) ")"
    fields = field ("," field)*
    field = identifier ":" type
    interval = ("tinterval" / "interval") _ "<" type ">"
    identifier = _ (simple_identifier / escaped_identifier) _
    simple_identifier = ~r"\w+"
    escaped_identifier = ~"`([^`\\\\]|\\\\.)*`"
    nat = _ (nat_literal / nat_variable) _
    nat_literal = ~"[0-9]+"
    nat_variable = "?nat"
    rng_state = "rng_state"
    _ = ~r"\s*"
    

Parameters:

    

**type_str** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – String representation of type.

Returns:

    

`HailType`

hail.expr.types.tint _ = dtype('int32')_

    

Alias for `tint32`.

hail.expr.types.tint32 _ = dtype('int32')_

    

Hail type for signed 32-bit integers.

Their values can range from \\(-2^{31}\\) to \\(2^{31} - 1\\) (approximately
2.15 billion).

In Python, these are represented as
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)").

See also

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression"),
[`int()`](functions/constructors.html#hail.expr.functions.int
"hail.expr.functions.int"),
[`int32()`](functions/constructors.html#hail.expr.functions.int32
"hail.expr.functions.int32")

hail.expr.types.tint64 _ = dtype('int64')_

    

Hail type for signed 64-bit integers.

Their values can range from \\(-2^{63}\\) to \\(2^{63} - 1\\).

In Python, these are represented as
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)").

See also

[`Int64Expression`](hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression"),
[`int64()`](functions/constructors.html#hail.expr.functions.int64
"hail.expr.functions.int64")

hail.expr.types.tfloat _ = dtype('float64')_

    

Alias for `tfloat64`.

hail.expr.types.tfloat32 _ = dtype('float32')_

    

Hail type for 32-bit floating point numbers.

In Python, these are represented as
[`float`](https://docs.python.org/3.9/library/functions.html#float "\(in
Python v3.9\)").

See also

[`Float32Expression`](hail.expr.Float32Expression.html#hail.expr.Float32Expression
"hail.expr.Float32Expression"),
[`float64()`](functions/constructors.html#hail.expr.functions.float64
"hail.expr.functions.float64")

hail.expr.types.tfloat64 _ = dtype('float64')_

    

Hail type for 64-bit floating point numbers.

In Python, these are represented as
[`float`](https://docs.python.org/3.9/library/functions.html#float "\(in
Python v3.9\)").

See also

[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression"),
[`float()`](functions/constructors.html#hail.expr.functions.float
"hail.expr.functions.float"),
[`float64()`](functions/constructors.html#hail.expr.functions.float64
"hail.expr.functions.float64")

hail.expr.types.tstr _ = dtype('str')_

    

Hail type for text strings.

In Python, these are represented as strings.

See also

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"),
[`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str")

hail.expr.types.tbool _ = dtype('bool')_

    

Hail type for Boolean (`True` or `False`) values.

In Python, these are represented as
[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)").

See also

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"),
[`bool()`](functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

_class
_hail.expr.types.tarray(_element_type_)[[source]](_modules/hail/expr/types.html#tarray)

    

Hail type for variable-length arrays of elements.

In Python, these are represented as
[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)").

Notes

Arrays contain elements of only one type, which is parameterized by
element_type.

Parameters:

    

**element_type** (`HailType`) – Element type of array.

See also

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"),
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"),
[`array()`](functions/constructors.html#hail.expr.functions.array
"hail.expr.functions.array"), [Collection
functions](functions/collections.html#sec-collection-functions)

_class _hail.expr.types.tndarray(_element_type_ ,
_ndim_)[[source]](_modules/hail/expr/types.html#tndarray)

    

Hail type for n-dimensional arrays.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

In Python, these are represented as NumPy
[`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray
"\(in NumPy v2.3\)").

Notes

NDArrays contain elements of only one type, which is parameterized by
element_type.

Parameters:

    

  * **element_type** (`HailType`) – Element type of array.

  * **ndim** (_int32_) – Number of dimensions.

See also

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression"), [`nd.array`](nd/index.html#hail.nd.array
"hail.nd.array")

_class
_hail.expr.types.tset(_element_type_)[[source]](_modules/hail/expr/types.html#tset)

    

Hail type for collections of distinct elements.

In Python, these are represented as
[`set`](https://docs.python.org/3.9/library/stdtypes.html#set "\(in Python
v3.9\)").

Notes

Sets contain elements of only one type, which is parameterized by
element_type.

Parameters:

    

**element_type** (`HailType`) – Element type of set.

See also

[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"),
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"),
[`set()`](functions/constructors.html#hail.expr.functions.set
"hail.expr.functions.set"), [Collection
functions](functions/collections.html#sec-collection-functions)

_class _hail.expr.types.tdict(_key_type_ ,
_value_type_)[[source]](_modules/hail/expr/types.html#tdict)

    

Hail type for key-value maps.

In Python, these are represented as
[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)").

Notes

Dicts parameterize the type of both their keys and values with key_type and
value_type.

Parameters:

    

  * **key_type** (`HailType`) – Key type.

  * **value_type** (`HailType`) – Value type.

See also

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"),
[`dict()`](functions/constructors.html#hail.expr.functions.dict
"hail.expr.functions.dict"), [Collection
functions](functions/collections.html#sec-collection-functions)

_class _hail.expr.types.tstruct(_**
field_types_)[[source]](_modules/hail/expr/types.html#tstruct)

    

Hail type for structured groups of heterogeneous fields.

In Python, these are represented as
```Struct```.

Hail’s `tstruct` type is commonly used to compose types together to form
nested structures. Structs can contain any combination of types, and are
ordered mappings from field name to field type. Each field name must be
unique.

Structs are very common in Hail. Each component of a
```Table``` and
```MatrixTable``` is
a struct:

  * ```Table.row()```

  * ```Table.globals()```

  * ```MatrixTable.row()```

  * ```MatrixTable.col()```

  * ```MatrixTable.entry()```

  * ```MatrixTable.globals()```

Structs appear below the top-level component types as well. Consider the
following join:

    
    
    >>> new_table = table1.annotate(table2_fields = table2.index(table1.key))
    

This snippet adds a field to `table1` called `table2_fields`. In the new
table, `table2_fields` will be a struct containing all the non-key fields from
`table2`.

Parameters:

    

**field_types** (keyword args of `HailType`) – Fields.

See also

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression"), [`Struct`](utils/index.html#hail.utils.Struct
"hail.utils.Struct")

_class _hail.expr.types.ttuple(_*
types_)[[source]](_modules/hail/expr/types.html#ttuple)

    

Hail type for tuples.

In Python, these are represented as
[`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python
v3.9\)").

Parameters:

    

**types** (varargs of `HailType`) – Element types.

See also

[`TupleExpression`](hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

hail.expr.types.tcall _ = dtype('call')_

    

Hail type for a diploid genotype.

In Python, these are represented by
[`Call`](genetics/hail.genetics.Call.html#hail.genetics.Call
"hail.genetics.Call").

See also

[`CallExpression`](hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression"),
[`Call`](genetics/hail.genetics.Call.html#hail.genetics.Call
"hail.genetics.Call"),
[`call()`](functions/genetics.html#hail.expr.functions.call
"hail.expr.functions.call"),
[`parse_call()`](functions/genetics.html#hail.expr.functions.parse_call
"hail.expr.functions.parse_call"),
[`unphased_diploid_gt_index_call()`](functions/genetics.html#hail.expr.functions.unphased_diploid_gt_index_call
"hail.expr.functions.unphased_diploid_gt_index_call")

_class _hail.expr.types.tlocus(_reference_genome
='default'_)[[source]](_modules/hail/expr/types.html#tlocus)

    

Hail type for a genomic coordinate with a contig and a position.

In Python, these are represented by
[`Locus`](genetics/hail.genetics.Locus.html#hail.genetics.Locus
"hail.genetics.Locus").

Parameters:

    

**reference_genome**
([`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome") or
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Reference genome to use.

See also

[`LocusExpression`](hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression"),
[`locus()`](functions/genetics.html#hail.expr.functions.locus
"hail.expr.functions.locus"),
[`parse_locus()`](functions/genetics.html#hail.expr.functions.parse_locus
"hail.expr.functions.parse_locus"),
[`Locus`](genetics/hail.genetics.Locus.html#hail.genetics.Locus
"hail.genetics.Locus")

reference_genome

    

Reference genome.

Returns:

    

[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome") – Reference genome.

_class
_hail.expr.types.tinterval(_point_type_)[[source]](_modules/hail/expr/types.html#tinterval)

    

Hail type for intervals of ordered values.

In Python, these are represented by
```Interval```.

Parameters:

    

**point_type** (`HailType`) – Interval point type.

See also

[`IntervalExpression`](hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression"),
```Interval```,
[`interval()`](functions/constructors.html#hail.expr.functions.interval
"hail.expr.functions.interval"),
[`parse_locus_interval()`](functions/genetics.html#hail.expr.functions.parse_locus_interval
"hail.expr.functions.parse_locus_interval")

---

# Functions

These functions are exposed at the top level of the module, e.g. `hl.case`.

  * ``Core language functions``
    * ```literal()```
    * ```cond()```
    * ```if_else()```
    * ```switch()```
    * ```case()```
    * ```bind()```
    * ```rbind()```
    * ```missing()```
    * ```null()```
    * ```str()```
    * ```is_missing()```
    * ```is_defined()```
    * ```coalesce()```
    * ```or_else()```
    * ```or_missing()```
    * ```range()```
    * ```query_table()```
    * ``ConditionalBuilder``
    * ``CaseBuilder``
    * ``SwitchBuilder``
  * ``Constructor functions``
    * ```bool()```
    * ```float()```
    * ```float32()```
    * ```float64()```
    * ```int()```
    * ```int32()```
    * ```int64()```
    * ```interval()```
    * ```struct()```
    * ```tuple()```
    * ```array()```
    * ```empty_array()```
    * ```set()```
    * ```empty_set()```
    * ```dict()```
    * ```empty_dict()```
  * ``Collection functions``
    * ```len()```
    * ```map()```
    * ```flatmap()```
    * ```starmap()```
    * ```zip()```
    * ```enumerate()```
    * ```zip_with_index()```
    * ```flatten()```
    * ```any()```
    * ```all()```
    * ```filter()```
    * ```sorted()```
    * ```find()```
    * ```group_by()```
    * ```fold()```
    * ```array_scan()```
    * ```reversed()```
    * ```keyed_intersection()```
    * ```keyed_union()```
  * ``Numeric functions``
    * ```abs()```
    * ```approx_equal()```
    * ```bit_and()```
    * ```bit_or()```
    * ```bit_xor()```
    * ```bit_lshift()```
    * ```bit_rshift()```
    * ```bit_not()```
    * ```bit_count()```
    * ```exp()```
    * ```expit()```
    * ```is_nan()```
    * ```is_finite()```
    * ```is_infinite()```
    * ```log()```
    * ```log10()```
    * ```logit()```
    * ```floor()```
    * ```ceil()```
    * ```sqrt()```
    * ```sign()```
    * ```min()```
    * ```nanmin()```
    * ```max()```
    * ```nanmax()```
    * ```mean()```
    * ```median()```
    * ```product()```
    * ```sum()```
    * ```cumulative_sum()```
    * ```argmin()```
    * ```argmax()```
    * ```corr()```
    * ```uniroot()```
    * ```binary_search()```
  * ``String functions``
    * ```format()```
    * ```json()```
    * ```parse_json()```
    * ```hamming()```
    * ```delimit()```
    * ```entropy()```
    * ```parse_int()```
    * ```parse_int32()```
    * ```parse_int64()```
    * ```parse_float()```
    * ```parse_float32()```
    * ```parse_float64()```
  * ``Statistical functions``
    * ```chi_squared_test()```
    * ```fisher_exact_test()```
    * ```contingency_table_test()```
    * ```cochran_mantel_haenszel_test()```
    * ```dbeta()```
    * ```dchisq()```
    * ```dnorm()```
    * ```dpois()```
    * ```hardy_weinberg_test()```
    * ```binom_test()```
    * ```pchisqtail()```
    * ```pgenchisq()```
    * ```pnorm()```
    * ```pT()```
    * ```pF()```
    * ```ppois()```
    * ```qchisqtail()```
    * ```qnorm()```
    * ```qpois()```
  * ``Random functions``
    * ``Setting a seed``
    * ``Reproducibility across sessions``
  * ``Genetics functions``
    * ```locus()```
    * ```locus_from_global_position()```
    * ```locus_interval()```
    * ```parse_locus()```
    * ```parse_variant()```
    * ```parse_locus_interval()```
    * ```variant_str()```
    * ```call()```
    * ```unphased_diploid_gt_index_call()```
    * ```parse_call()```
    * ```downcode()```
    * ```triangle()```
    * ```is_snp()```
    * ```is_mnp()```
    * ```is_transition()```
    * ```is_transversion()```
    * ```is_insertion()```
    * ```is_deletion()```
    * ```is_indel()```
    * ```is_star()```
    * ```is_complex()```
    * ```is_strand_ambiguous()```
    * ```is_valid_contig()```
    * ```is_valid_locus()```
    * ```contig_length()```
    * ```allele_type()```
    * ```numeric_allele_type()```
    * ```pl_dosage()```
    * ```gp_dosage()```
    * ```get_sequence()```
    * ```mendel_error_code()```
    * ```liftover()```
    * ```min_rep()```
    * ```reverse_complement()```

Core language functions

```literal```(x[, dtype]) | Captures and broadcasts a Python variable or object as an expression.  
---|---  
```cond```(condition, consequent, alternate[, ...]) | Deprecated in favor of ```if_else()```.  
```if_else```(condition, consequent, alternate[, ...]) | Expression for an if/else statement; tests a condition and returns one of two options based on the result.  
```switch```(expr) | Build a conditional tree on the value of an expression.  
```case```([missing_false]) | Chain multiple if-else statements with a ```CaseBuilder```.  
```bind```(f, *exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
```rbind```(*exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
```null```(t) | Deprecated in favor of ```missing()```.  
```is_missing```(expression) | Returns `True` if the argument is missing.  
```is_defined```(expression) | Returns `True` if the argument is not missing.  
```coalesce```(*args) | Returns the first non-missing value of args.  
```or_else```(a, b) | If a is missing, return b.  
```or_missing```(predicate, value) | Returns value if predicate is `True`, otherwise returns missing.  
```range```(start[, stop, step]) | Returns an array of integers from start to stop by step.  
```query_table```(path, point_or_interval) | Query records from a table corresponding to a given point or range of keys.  
  
Constructors

```bool```(x) | Convert to a Boolean expression.  
---|---  
```float```(x) | Convert to a 64-bit floating point expression.  
```float32```(x) | Convert to a 32-bit floating point expression.  
```float64```(x) | Convert to a 64-bit floating point expression.  
```int```(x) | Convert to a 32-bit integer expression.  
```int32```(x) | Convert to a 32-bit integer expression.  
```int64```(x) | Convert to a 64-bit integer expression.  
```interval```(start, end[, includes_start, ...]) | Construct an interval expression.  
```str```(x) | Returns the string representation of x.  
```struct```(**kwargs) | Construct a struct expression.  
```tuple```(iterable) | Construct a tuple expression.  
  
Collection constructors

```array```(collection) | Construct an array expression.  
---|---  
```empty_array```(t) | Returns an empty array of elements of a type t.  
```set```(collection) | Convert a set expression.  
```empty_set```(t) | Returns an empty set of elements of a type t.  
```dict```(collection) | Creates a dictionary.  
```empty_dict```(key_type, value_type) | Returns an empty dictionary with key type key_type and value type value_type.  
  
Collection functions

```len```(x) | Returns the size of a collection or string.  
---|---  
```map```(f, *collections) | Transform each element of a collection.  
```flatmap```(f, collection) | Map each element of the collection to a new collection, and flatten the results.  
```zip```(*arrays[, fill_missing]) | Zip together arrays into a single array.  
```enumerate```(a[, start, index_first]) | Returns an array of (index, element) tuples.  
```zip_with_index```(a[, index_first]) | Deprecated in favor of ```enumerate()```.  
```flatten```(collection) | Flatten a nested collection by concatenating sub-collections.  
```any```(*args) | Check for any `True` in boolean expressions or collections of booleans.  
```all```(*args) | Check for all `True` in boolean expressions or collections of booleans.  
```filter```(f, collection) | Returns a new collection containing elements where f returns `True`.  
```sorted```(collection[, key, reverse]) | Returns a sorted array.  
```find```(f, collection) | Returns the first element where f returns `True`.  
```group_by```(f, collection) | Group collection elements into a dict according to a lambda function.  
```fold```(f, zero, collection) | Reduces a collection with the given function f, provided the initial value zero.  
```array_scan```(f, zero, a) | Map each element of a to cumulative value of function f, with initial value zero.  
```reversed```(x) | Reverses the elements of a collection.  
```keyed_intersection```(*arrays, key) | Compute the intersection of sorted arrays on a given key.  
```keyed_union```(*arrays, key) | Compute the distinct union of sorted arrays on a given key.  
  
Numeric functions

```abs```(x) | Take the absolute value of a numeric value, array or ndarray.  
---|---  
```approx_equal```(x, y[, tolerance, absolute, ...]) | Tests whether two numbers are approximately equal.  
```bit_and```(x, y) | Bitwise and x and y.  
```bit_or```(x, y) | Bitwise or x and y.  
```bit_xor```(x, y) | Bitwise exclusive-or x and y.  
```bit_lshift```(x, y) | Bitwise left-shift x by y.  
```bit_rshift```(x, y[, logical]) | Bitwise right-shift x by y.  
```bit_not```(x) | Bitwise invert x.  
```bit_count```(x) | Count the number of 1s in the in the [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) binary representation of x.  
```exp```(x) |   
```expit```(x) |   
```is_nan```(x) |   
```is_finite```(x) |   
```is_infinite```(x) |   
```log```(x[, base]) | Take the logarithm of the x with base base.  
```log10```(x) |   
```logit```(x) |   
```sign```(x) | Returns the sign of a numeric value, array or ndarray.  
```sqrt```(x) |   
```int```(x) | Convert to a 32-bit integer expression.  
```int32```(x) | Convert to a 32-bit integer expression.  
```int64```(x) | Convert to a 64-bit integer expression.  
```float```(x) | Convert to a 64-bit floating point expression.  
```float32```(x) | Convert to a 32-bit floating point expression.  
```float64```(x) | Convert to a 64-bit floating point expression.  
```floor```(x) |   
```ceil```(x) |   
```uniroot```(f, min, max, *[, max_iter, epsilon, ...]) | Finds a root of the function f within the interval [min, max].  
  
Numeric collection functions

```min```(*exprs[, filter_missing]) | Returns the minimum element of a collection or of given numeric expressions.  
---|---  
```nanmin```(*exprs[, filter_missing]) | Returns the minimum value of a collection or of given arguments, excluding NaN.  
```max```(*exprs[, filter_missing]) | Returns the maximum element of a collection or of given numeric expressions.  
```nanmax```(*exprs[, filter_missing]) | Returns the maximum value of a collection or of given arguments, excluding NaN.  
```mean```(collection[, filter_missing]) | Returns the mean of all values in the collection.  
```median```(collection) | Returns the median value in the collection.  
```product```(collection[, filter_missing]) | Returns the product of values in the collection.  
```sum```(collection[, filter_missing]) | Returns the sum of values in the collection.  
```cumulative_sum```(a[, filter_missing]) | Returns an array of the cumulative sum of values in the array.  
```argmin```(array[, unique]) | Return the index of the minimum value in the array.  
```argmax```(array[, unique]) | Return the index of the maximum value in the array.  
```corr```(x, y) | Compute the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between x and y.  
```binary_search```(array, elem) | Binary search array for the insertion point of elem.  
  
String functions

```format```(f, *args) | Returns a formatted string using a specified format string and arguments.  
---|---  
```json```(x) | Convert an expression to a JSON string expression.  
```parse_json```(x, dtype) | Convert a JSON string to a structured expression.  
```hamming```(s1, s2) | Returns the Hamming distance between the two strings.  
```delimit```(collection[, delimiter]) | Joins elements of collection into single string delimited by delimiter.  
```entropy```(s) | Returns the [Shannon entropy](https://en.wikipedia.org/wiki/Entropy_\(information_theory\)) of the character distribution defined by the string.  
```parse_int```(x) | Parse a string as a 32-bit integer.  
```parse_int32```(x) | Parse a string as a 32-bit integer.  
```parse_int64```(x) | Parse a string as a 64-bit integer.  
```parse_float```(x) | Parse a string as a 64-bit floating point number.  
```parse_float32```(x) | Parse a string as a 32-bit floating point number.  
```parse_float64```(x) | Parse a string as a 64-bit floating point number.  
  
Statistical functions

```chi_squared_test```(c1, c2, c3, c4) | Performs chi-squared test of independence on a 2x2 contingency table.  
---|---  
```fisher_exact_test```(c1, c2, c3, c4) | Calculates the p-value, odds ratio, and 95% confidence interval using Fisher's exact test for a 2x2 table.  
```contingency_table_test```(c1, c2, c3, c4, ...) | Performs chi-squared or Fisher's exact test of independence on a 2x2 contingency table.  
```cochran_mantel_haenszel_test```(a, b, c, d) | Perform the Cochran-Mantel-Haenszel test for association.  
```dbeta```(x, a, b) | Returns the probability density at x of a [beta distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters a (alpha) and b (beta).  
```dpois```(x, lamb[, log_p]) | Compute the (log) probability density at x of a Poisson distribution with rate parameter lamb.  
```hardy_weinberg_test```(n_hom_ref, n_het, n_hom_var) | Performs test of Hardy-Weinberg equilibrium.  
```pchisqtail```(x, df[, ncp, lower_tail, log_p]) | Returns the probability under the right-tail starting at x for a chi-squared distribution with df degrees of freedom.  
```pnorm```(x[, mu, sigma, lower_tail, log_p]) | The cumulative probability function of a normal distribution with mean mu and standard deviation sigma.  
```ppois```(x, lamb[, lower_tail, log_p]) | The cumulative probability function of a Poisson distribution.  
```qchisqtail```(p, df[, ncp, lower_tail, log_p]) | The quantile function of a chi-squared distribution with df degrees of freedom, inverts ```pchisqtail()```.  
```qnorm```(p[, mu, sigma, lower_tail, log_p]) | The quantile function of a normal distribution with mean mu and standard deviation sigma, inverts ```pnorm()```.  
```qpois```(p, lamb[, lower_tail, log_p]) | The quantile function of a Poisson distribution with rate parameter lamb, inverts ```ppois()```.  
  
Randomness

```rand_bool```(p[, seed]) | Returns `True` with probability p.  
---|---  
```rand_beta```(a, b[, lower, upper, seed]) | Samples from a [beta distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters a (alpha) and b (beta).  
```rand_cat```(prob[, seed]) | Samples from a [categorical distribution](https://en.wikipedia.org/wiki/Categorical_distribution).  
```rand_dirichlet```(a[, seed]) | Samples from a [Dirichlet distribution](https://en.wikipedia.org/wiki/Dirichlet_distribution).  
```rand_gamma```(shape, scale[, seed]) | Samples from a [gamma distribution](https://en.wikipedia.org/wiki/Gamma_distribution) with parameters shape and scale.  
```rand_norm```([mean, sd, seed, size]) | Samples from a normal distribution with mean mean and standard deviation sd.  
```rand_pois```(lamb[, seed]) | Samples from a [Poisson distribution](https://en.wikipedia.org/wiki/Poisson_distribution) with rate parameter lamb.  
```rand_unif```([lower, upper, seed, size]) | Samples from a uniform distribution within the interval [lower, upper].  
```rand_int32```(a[, b, seed]) | Samples from a uniform distribution of 32-bit integers.  
```rand_int64```([a, b, seed]) | Samples from a uniform distribution of 64-bit integers.  
```shuffle```(a[, seed]) | Randomly permute an array  
  
Genetics functions

```locus```(contig, pos[, reference_genome]) | Construct a locus expression from a chromosome and position.  
---|---  
```locus_from_global_position```(global_pos[, ...]) | Constructs a locus expression from a global position and a reference genome.  
```locus_interval```(contig, start, end[, ...]) | Construct a locus interval expression.  
```parse_locus```(s[, reference_genome]) | Construct a locus expression by parsing a string or string expression.  
```parse_variant```(s[, reference_genome]) | Construct a struct with a locus and alleles by parsing a string.  
```parse_locus_interval```(s[, reference_genome, ...]) | Construct a locus interval expression by parsing a string or string expression.  
```variant_str```(*args) | Create a variant colon-delimited string.  
```call```(*alleles[, phased]) | Construct a call expression.  
```unphased_diploid_gt_index_call```(gt_index) | Construct an unphased, diploid call from a genotype index.  
```parse_call```(s) | Construct a call expression by parsing a string or string expression.  
```downcode```(c, i) | Create a new call by setting all alleles other than i to ref  
```triangle```(n) | Returns the triangle number of n.  
```is_snp```(ref, alt) | Returns `True` if the alleles constitute a single nucleotide polymorphism.  
```is_mnp```(ref, alt) | Returns `True` if the alleles constitute a multiple nucleotide polymorphism.  
```is_transition```(ref, alt) | Returns `True` if the alleles constitute a transition.  
```is_transversion```(ref, alt) | Returns `True` if the alleles constitute a transversion.  
```is_insertion```(ref, alt) | Returns `True` if the alleles constitute an insertion.  
```is_deletion```(ref, alt) | Returns `True` if the alleles constitute a deletion.  
```is_indel```(ref, alt) | Returns `True` if the alleles constitute an insertion or deletion.  
```is_star```(ref, alt) | Returns `True` if the alleles constitute an upstream deletion.  
```is_complex```(ref, alt) | Returns `True` if the alleles constitute a complex polymorphism.  
```is_valid_contig```(contig[, reference_genome]) | Returns `True` if contig is a valid contig name in reference_genome.  
```is_valid_locus```(contig, position[, ...]) | Returns `True` if contig and position is a valid site in reference_genome.  
```contig_length```(contig[, reference_genome]) | Returns the length of contig in reference_genome.  
```allele_type```(ref, alt) | Returns the type of the polymorphism as a string.  
```pl_dosage```(pl) | Return expected genotype dosage from array of Phred-scaled genotype likelihoods with uniform prior.  
```gp_dosage```(gp) | Return expected genotype dosage from array of genotype probabilities.  
```get_sequence```(contig, position[, before, ...]) | Return the reference sequence at a given locus.  
```mendel_error_code```(locus, is_female, father, ...) | Compute a Mendelian violation code for genotypes.  
```liftover```(x, dest_reference_genome[, ...]) | Lift over coordinates to a different reference genome.  
```min_rep```(locus, alleles) | Computes the minimal representation of a (locus, alleles) polymorphism.  
```reverse_complement```(s[, rna]) | Reverses the string and translates base pairs into their complements .

---

# Aggregators  
  
The `aggregators` module is exposed as `hl.agg`, e.g. `hl.agg.sum`.

`collect`(expr) | Collect records into an array.  
---|---  
`collect_as_set`(expr) | Collect records into a set.  
`count`() | Count the number of records.  
`count_where`(condition) | Count the number of records where a predicate is `True`.  
`counter`(expr, *[, weight]) | Count the occurrences of each unique record and return a dictionary.  
`any`(condition) | Returns `True` if condition is `True` for any record.  
`all`(condition) | Returns `True` if condition is `True` for every record.  
`take`(expr, n[, ordering]) | Take n records of expr, optionally ordered by ordering.  
`min`(expr) | Compute the minimum expr.  
`max`(expr) | Compute the maximum expr.  
`sum`(expr) | Compute the sum of all records of expr.  
`array_sum`(expr) | Compute the coordinate-wise sum of all records of expr.  
`mean`(expr) | Compute the mean value of records of expr.  
`approx_quantiles`(expr, qs[, k]) | Compute an array of approximate quantiles.  
`approx_median`(expr[, k]) | Compute the approximate median.  
`stats`(expr) | Compute a number of useful statistics about expr.  
`product`(expr) | Compute the product of all records of expr.  
`fraction`(predicate) | Compute the fraction of records where predicate is `True`.  
`hardy_weinberg_test`(expr[, one_sided]) | Performs test of Hardy-Weinberg equilibrium.  
`explode`(f, array_agg_expr) | Explode an array or set expression to aggregate the elements of all records.  
`filter`(condition, aggregation) | Filter records according to a predicate.  
`inbreeding`(expr, prior) | Compute inbreeding statistics on calls.  
`call_stats`(call, alleles) | Compute useful call statistics.  
`info_score`(gp) | Compute the IMPUTE information score.  
`hist`(expr, start, end, bins) | Compute binned counts of a numeric expression.  
`linreg`(y, x[, nested_dim, weight]) | Compute multivariate linear regression statistics.  
`corr`(x, y) | Computes the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between x and y.  
`group_by`(group, agg_expr) | Compute aggregation statistics stratified by one or more groups.  
`array_agg`(f, array) | Aggregate an array element-wise using a user-specified aggregation function.  
`downsample`(x, y[, label, n_divisions]) | Downsample (x, y) coordinate datapoints.  
`approx_cdf`(expr[, k, _raw]) | Produce a summary of the distribution of values.  
  
hail.expr.aggregators.collect(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#collect)

    

Collect records into an array.

Examples

Collect the ID field where HT is greater than 68:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect(table1.ID)))
    [2, 3]
    

Notes

The element order of the resulting array is not guaranteed, and in some cases
is non-deterministic.

Use `collect_as_set()` to collect unique items.

Warning

Collecting a large number of items can cause out-of-memory exceptions.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression to collect.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of all expr records.

hail.expr.aggregators.collect_as_set(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#collect_as_set)

    

Collect records into a set.

Examples

Collect the unique ID field where HT is greater than 68:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect_as_set(table1.ID)))
    {2, 3}
    

Note that when collecting a set-typed field with `collect_as_set()`, the
values become
[`frozenset`](https://docs.python.org/3.9/library/stdtypes.html#frozenset
"\(in Python v3.9\)") s because Python does not permit the keys of a
dictionary to be mutable:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT > 68, hl.agg.collect_as_set(hl.set({table1.ID}))))
    {frozenset({3}), frozenset({2})}
    

Warning

Collecting a large number of items can cause out-of-memory exceptions.

Parameters:

    

**expr** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression to collect.

Returns:

    

[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Set of unique expr records.

hail.expr.aggregators.count()[[source]](_modules/hail/expr/aggregators/aggregators.html#count)

    

Count the number of records.

Examples

Group by the SEX field and count the number of rows in each category:

    
    
    >>> (table1.group_by(table1.SEX)
    ...        .aggregate(n=hl.agg.count())
    ...        .show())
    +-----+-------+
    | SEX |     n |
    +-----+-------+
    | str | int64 |
    +-----+-------+
    | "F" |     2 |
    | "M" |     2 |
    +-----+-------+
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") – Total number of records.

hail.expr.aggregators.count_where(_condition_)[[source]](_modules/hail/expr/aggregators/aggregators.html#count_where)

    

Count the number of records where a predicate is `True`.

Examples

Count the number of individuals with HT greater than 68:

    
    
    >>> table1.aggregate(hl.agg.count_where(table1.HT > 68))
    2
    

Parameters:

    

**condition**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Criteria for inclusion.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") – Total number of records where condition is `True`.

hail.expr.aggregators.counter(_expr_ , _*_ , _weight
=None_)[[source]](_modules/hail/expr/aggregators/aggregators.html#counter)

    

Count the occurrences of each unique record and return a dictionary.

Examples

Count the number of individuals for each unique SEX value:

    
    
    >>> table1.aggregate(hl.agg.counter(table1.SEX))
    {'F': 2, 'M': 2}
    

Compute the total height for each unique SEX value:

    
    
    >>> table1.aggregate(hl.agg.counter(table1.SEX, weight=table1.HT))
    {'F': 130, 'M': 137}
    

Note that when counting a set-typed field, the values become
[`frozenset`](https://docs.python.org/3.9/library/stdtypes.html#frozenset
"\(in Python v3.9\)") s because Python does not permit the keys of a
dictionary to be mutable:

    
    
    >>> table1.aggregate(hl.agg.counter(hl.set({table1.SEX}), weight=table1.HT))
    {frozenset({'F'}): 130, frozenset({'M'}): 137}
    

Notes

If you need a more complex grouped aggregation than `counter()` supports, try
using `group_by()`.

This aggregator method returns a dict expression whose key type is the same
type as expr and whose value type is
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64"). This dict contains a key for each unique value of
expr, and the value is the number of times that key was observed.

Ensure that the result can be stored in memory on a single machine.

Warning

Using `counter()` with a large number of unique items can cause out-of-memory
exceptions.

Parameters:

    

  * **expr** (```Expression```) – Expression to count by key.

  * **weight** (```NumericExpression```, optional) – Expression by which to weight each occurence (when unspecified, it is effectively `1`)

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") – Dictionary with the number of occurrences of
each unique record.

hail.expr.aggregators.any(_condition_)[[source]](_modules/hail/expr/aggregators/aggregators.html#any)

    

Returns `True` if condition is `True` for any record.

Examples

    
    
    >>> (table1.group_by(table1.SEX)
    ... .aggregate(any_over_70 = hl.agg.any(table1.HT > 70))
    ... .show())
    +-----+-------------+
    | SEX | any_over_70 |
    +-----+-------------+
    | str | bool        |
    +-----+-------------+
    | "F" | False       |
    | "M" | True        |
    +-----+-------------+
    

Notes

If there are no records to aggregate, the result is `False`.

Missing records are not considered. If every record is missing, the result is
also `False`.

Parameters:

    

**condition**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Condition to test.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.aggregators.all(_condition_)[[source]](_modules/hail/expr/aggregators/aggregators.html#all)

    

Returns `True` if condition is `True` for every record.

Examples

    
    
    >>> (table1.group_by(table1.SEX)
    ... .aggregate(all_under_70 = hl.agg.all(table1.HT < 70))
    ... .show())
    +-----+--------------+
    | SEX | all_under_70 |
    +-----+--------------+
    | str | bool         |
    +-----+--------------+
    | "F" | False        |
    | "M" | False        |
    +-----+--------------+
    

Notes

If there are no records to aggregate, the result is `True`.

Missing records are not considered. If every record is missing, the result is
also `True`.

Parameters:

    

**condition**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Condition to test.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.aggregators.take(_expr_ , _n_ , _ordering
=None_)[[source]](_modules/hail/expr/aggregators/aggregators.html#take)

    

Take n records of expr, optionally ordered by ordering.

Examples

Take 3 elements of field X:

    
    
    >>> table1.aggregate(hl.agg.take(table1.X, 3))
    [5, 6, 7]
    

Take the ID and HT fields, ordered by HT (descending):

    
    
    >>> table1.aggregate(hl.agg.take(hl.struct(ID=table1.ID, HT=table1.HT),
    ...                              3,
    ...                              ordering=-table1.HT))
    [Struct(ID=2, HT=72), Struct(ID=3, HT=70), Struct(ID=1, HT=65)]
    

Notes

The resulting array can include fewer than n elements if there are fewer than
n total records.

The ordering argument may be an expression, a function, or `None`.

If ordering is an expression, this expression’s type should be one with a
natural ordering (e.g. numeric).

If ordering is a function, it will be evaluated on each record of expr to
compute the value used for ordering. In the above example,
`ordering=-table1.HT` and `ordering=lambda x: -x.HT` would be equivalent.

If ordering is `None`, then there is no guaranteed ordering on the elements
taken, and and the results may be non-deterministic.

Missing values are always sorted **last**.

Parameters:

    

  * **expr** (```Expression```) – Expression to store.

  * **n** (```Expression``` of type ```tint32```) – Number of records to take.

  * **ordering** (```Expression``` or function ((arg) -> ```Expression```) or None) – Optional ordering on records.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of up to n records of expr.

hail.expr.aggregators.min(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#min)

    

Compute the minimum expr.

Examples

Compute the minimum value of HT:

    
    
    >>> table1.aggregate(hl.agg.min(table1.HT))
    60
    

Notes

This function returns the minimum non-missing value. If there are no non-
missing values, then the result is missing.

For back-compatibility reasons, this function also ignores NaN, in contrast
with [`functions.min()`](functions/numeric.html#hail.expr.functions.min
"hail.expr.functions.min"). The behavior is similar to
[`functions.nanmin()`](functions/numeric.html#hail.expr.functions.nanmin
"hail.expr.functions.nanmin").

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Minimum value of all expr records, same type
as expr.

hail.expr.aggregators.max(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#max)

    

Compute the maximum expr.

Examples

Compute the maximum value of HT:

    
    
    >>> table1.aggregate(hl.agg.max(table1.HT))
    72
    

Notes

This function returns the maximum non-missing value. If there are no non-
missing values, then the result is missing.

For back-compatibility reasons, this function also ignores NaN, in contrast
with [`functions.max()`](functions/numeric.html#hail.expr.functions.max
"hail.expr.functions.max"). The behavior is similar to
[`functions.nanmax()`](functions/numeric.html#hail.expr.functions.nanmax
"hail.expr.functions.nanmax").

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Maximum value of all expr records, same type
as expr.

hail.expr.aggregators.sum(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#sum)

    

Compute the sum of all records of expr.

Examples

Compute the sum of field C1:

    
    
    >>> table1.aggregate(hl.agg.sum(table1.C1))
    25
    

Notes

Missing values are ignored (treated as zero).

If expr is an expression of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32"), [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64"), or [`tbool`](types.html#hail.expr.types.tbool
"hail.expr.types.tbool"), then the result is an expression of type
```tint64```. If
expr is an expression of type [`tfloat32`](types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32") or
```tfloat64```,
then the result is an expression of type
```tfloat64```.

Warning

Boolean values are cast to integers before computing the sum.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or [`tfloat64`](types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – Sum of records of expr.

hail.expr.aggregators.array_sum(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#array_sum)

    

Compute the coordinate-wise sum of all records of expr.

Examples

Compute the sum of C1 and C2:

    
    
    >>> table1.aggregate(hl.agg.array_sum([table1.C1, table1.C2]))
    [25, 282]
    

Notes

All records must have the same length. Each coordinate is summed independently
as described in `sum()`.

Parameters:

    

**expr**
([`ArrayNumericExpression`](hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression"))

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") with element type
```tint64``` or
```tfloat64```

hail.expr.aggregators.mean(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#mean)

    

Compute the mean value of records of expr.

Examples

Compute the mean of field HT:

    
    
    >>> table1.aggregate(hl.agg.mean(table1.HT))
    66.75
    

Notes

Missing values are ignored.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Mean value of records of expr.

hail.expr.aggregators.approx_quantiles(_expr_ , _qs_ , _k
=100_)[[source]](_modules/hail/expr/aggregators/aggregators.html#approx_quantiles)

    

Compute an array of approximate quantiles.

Examples

Estimate the median of the HT field.

    
    
    >>> table1.aggregate(hl.agg.approx_quantiles(table1.HT, 0.5)) 
    64
    

Estimate the quartiles of the HT field.

    
    
    >>> table1.aggregate(hl.agg.approx_quantiles(table1.HT, [0, 0.25, 0.5, 0.75, 1])) 
    [50, 60, 64, 71, 86]
    

Warning

This is an approximate and nondeterministic method.

Parameters:

    

  * **expr** (```Expression```) – Expression to collect.

  * **qs** (```NumericExpression``` or ```ArrayNumericExpression```) – Number or array of numbers between 0 and 1.

  * **k** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Parameter controlling the accuracy vs. memory usage tradeoff. Increasing k increases both memory use and accuracy.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`ArrayNumericExpression`](hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") – If qs is a single number, returns the
estimated quantile. If qs is an array, returns the array of estimated
quantiles.

hail.expr.aggregators.approx_median(_expr_ , _k
=100_)[[source]](_modules/hail/expr/aggregators/aggregators.html#approx_median)

    

Compute the approximate median. This function is a shorthand for
approx_quantiles(expr, .5, k)

Examples

Estimate the median of the HT field.

    
    
    >>> table1.aggregate(hl.agg.approx_median(table1.HT)) 
    64
    

Warning

This is an approximate and nondeterministic method.

Parameters:

    

  * **expr** (```Expression```) – Expression to collect.

  * **k** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Parameter controlling the accuracy vs. memory usage tradeoff. Increasing k increases both memory use and accuracy.

See also

`approx_quantiles()`

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The estimated median.

hail.expr.aggregators.stats(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#stats)

    

Compute a number of useful statistics about expr.

Examples

Compute statistics about field HT:

    
    
    >>> table1.aggregate(hl.agg.stats(table1.HT))  
    Struct(mean=66.75, stdev=4.656984002549289, min=60.0, max=72.0, n=4, sum=267.0)
    

Notes

Computes a struct with the following fields:

  * min (```tfloat64```) - Minimum value.

  * max (```tfloat64```) - Maximum value.

  * mean (```tfloat64```) - Mean value,

  * stdev (```tfloat64```) - Standard deviation.

  * n (```tint64```) - Number of non-missing records.

  * sum (```tfloat64```) - Sum.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields mean, stdev,
min, max, n, and sum.

hail.expr.aggregators.product(_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#product)

    

Compute the product of all records of expr.

Examples

Compute the product of field C1:

    
    
    >>> table1.aggregate(hl.agg.product(table1.C1))
    440
    

Notes

Missing values are ignored (treated as one).

If expr is an expression of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32"), [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or [`tbool`](types.html#hail.expr.types.tbool
"hail.expr.types.tbool"), then the result is an expression of type
```tint64```. If
expr is an expression of type [`tfloat32`](types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32") or
```tfloat64```,
then the result is an expression of type
```tfloat64```.

Warning

Boolean values are cast to integers before computing the product.

Parameters:

    

**expr**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Numeric expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or [`tfloat64`](types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – Product of records of expr.

hail.expr.aggregators.fraction(_predicate_)[[source]](_modules/hail/expr/aggregators/aggregators.html#fraction)

    

Compute the fraction of records where predicate is `True`.

Examples

Compute the fraction of rows where SEX is “F” and HT > 65:

    
    
    >>> table1.aggregate(hl.agg.fraction((table1.SEX == 'F') & (table1.HT > 65)))
    0.25
    

Notes

Missing values for predicate are treated as `False`.

Parameters:

    

**predicate**
([`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")) – Boolean predicate.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Fraction of records where predicate is `True`.

hail.expr.aggregators.hardy_weinberg_test(_expr_ , _one_sided
=False_)[[source]](_modules/hail/expr/aggregators/aggregators.html#hardy_weinberg_test)

    

Performs test of Hardy-Weinberg equilibrium.

Examples

Test each row of a dataset:

    
    
    >>> dataset_result = dataset.annotate_rows(hwe = hl.agg.hardy_weinberg_test(dataset.GT))
    

Test each row on a sub-population:

    
    
    >>> dataset_result = dataset.annotate_rows(
    ...     hwe_eas = hl.agg.filter(dataset.pop == 'EAS',
    ...                             hl.agg.hardy_weinberg_test(dataset.GT)))
    

Notes

This method performs the test described in
[`functions.hardy_weinberg_test()`](functions/stats.html#hail.expr.functions.hardy_weinberg_test
"hail.expr.functions.hardy_weinberg_test") based solely on the counts of
homozygous reference, heterozygous, and homozygous variant calls.

The resulting struct expression has two fields:

  * het_freq_hwe (```tfloat64```) - Expected frequency of heterozygous calls under Hardy-Weinberg equilibrium.

  * p_value (```tfloat64```) - p-value from test of Hardy-Weinberg equilibrium.

By default, Hail computes the exact p-value with mid-p-value correction, i.e.
the probability of a less-likely outcome plus one-half the probability of an
equally-likely outcome. See this [document](_static/LeveneHaldane.pdf) for
details on the Levene-Haldane distribution and references.

To perform one-sided exact test of excess heterozygosity with mid-p-value
correction instead, set one_sided=True and the p-value returned will be from
the one-sided exact test.

Warning

Non-diploid calls (`ploidy != 2`) are ignored in the counts. While the counts
are defined for multiallelic variants, this test is only statistically
rigorous in the biallelic setting; use
[`split_multi()`](methods/genetics.html#hail.methods.split_multi
"hail.methods.split_multi") to split multiallelic variants beforehand.

Parameters:

    

  * **expr** (```CallExpression```) – Call to test for Hardy-Weinberg equilibrium.

  * **one_sided** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – `False` by default. When `True`, perform one-sided test for excess heterozygosity.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields het_freq_hwe and
p_value.

hail.expr.aggregators.explode(_f_ ,
_array_agg_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#explode)

    

Explode an array or set expression to aggregate the elements of all records.

Examples

Compute the mean of all elements in fields C1, C2, and C3:

    
    
    >>> table1.aggregate(hl.agg.explode(lambda elt: hl.agg.mean(elt), [table1.C1, table1.C2, table1.C3]))
    24.833333333333332
    

Compute the set of all observed elements in the filters field (`Set[String]`):

    
    
    >>> dataset.aggregate_rows(hl.agg.explode(lambda elt: hl.agg.collect_as_set(elt), dataset.filters))
    set()
    

Notes

This method can be used with aggregator functions to aggregate the elements of
collection types ([`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray") and [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset")).

Parameters:

    

  * **f** (Function from ```Expression``` to ```Expression```) – Aggregation function to apply to each element of the exploded array.

  * **array_agg_expr** (```CollectionExpression```) – Expression of type ```tarray``` or ```tset```.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Aggregation expression.

hail.expr.aggregators.filter(_condition_ ,
_aggregation_)[[source]](_modules/hail/expr/aggregators/aggregators.html#filter)

    

Filter records according to a predicate.

Examples

Collect the ID field where HT >= 70:

    
    
    >>> table1.aggregate(hl.agg.filter(table1.HT >= 70, hl.agg.collect(table1.ID)))
    [2, 3]
    

Notes

This method can be used with aggregator functions to remove records from
aggregation.

Parameters:

    

  * **condition** (```BooleanExpression```) – Filter expression.

  * **aggregation** (```Expression```) – Aggregation expression to filter.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Aggregable expression.

hail.expr.aggregators.inbreeding(_expr_ ,
_prior_)[[source]](_modules/hail/expr/aggregators/aggregators.html#inbreeding)

    

Compute inbreeding statistics on calls.

Examples

Compute inbreeding statistics per column:

    
    
    >>> dataset_result = dataset.annotate_cols(IB = hl.agg.inbreeding(dataset.GT, dataset.variant_qc.AF[1]))
    >>> dataset_result.IB.show(width=100)
    +------------------+-----------+-------------+------------------+------------------+
    | s                | IB.f_stat | IB.n_called | IB.expected_homs | IB.observed_homs |
    +------------------+-----------+-------------+------------------+------------------+
    | str              |   float64 |       int64 |          float64 |            int64 |
    +------------------+-----------+-------------+------------------+------------------+
    | "C1046::HG02024" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1046::HG02025" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1046::HG02026" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1047::HG00731" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1047::HG00732" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1047::HG00733" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    | "C1048::HG02024" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1048::HG02025" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1048::HG02026" | -4.41e-01 |           9 |         7.61e+00 |                7 |
    | "C1049::HG00731" |  2.79e-01 |           9 |         7.61e+00 |                8 |
    +------------------+-----------+-------------+------------------+------------------+
    showing top 10 rows
    

Notes

`E` is total number of expected homozygous calls, given by the sum of `1 - 2.0
* prior * (1 - prior)` across records.

`O` is the observed number of homozygous calls across records.

`N` is the number of non-missing calls.

`F` is the inbreeding coefficient, and is computed by: `(O - E) / (N - E)`.

This method returns a struct expression with four fields:

>   * f_stat ([`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")): `F`, the inbreeding coefficient.
>
>   * n_called ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): `N`, the number of non-missing calls.
>
>   * expected_homs ([`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")): `E`, the expected number of homozygotes.
>
>   * observed_homs ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): `O`, the number of observed homozygotes.
>
>

Parameters:

    

  * **expr** (```CallExpression```) – Call expression.

  * **prior** (```Expression``` of type ```tfloat64```) – Alternate allele frequency prior.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields f_stat,
n_called, expected_homs, observed_homs.

hail.expr.aggregators.call_stats(_call_ ,
_alleles_)[[source]](_modules/hail/expr/aggregators/aggregators.html#call_stats)

    

Compute useful call statistics.

Examples

Compute call statistics per row:

    
    
    >>> dataset_result = dataset.annotate_rows(gt_stats = hl.agg.call_stats(dataset.GT, dataset.alleles))
    >>> dataset_result.rows().key_by('locus').select('gt_stats').show(width=120)
    +---------------+--------------+---------------------+-------------+---------------------------+
    | locus         | gt_stats.AC  | gt_stats.AF         | gt_stats.AN | gt_stats.homozygote_count |
    +---------------+--------------+---------------------+-------------+---------------------------+
    | locus<GRCh37> | array<int32> | array<float64>      |       int32 | array<int32>              |
    +---------------+--------------+---------------------+-------------+---------------------------+
    | 20:10579373   | [199,1]      | [9.95e-01,5.00e-03] |         200 | [99,0]                    |
    | 20:10579398   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [99,1]                    |
    | 20:10627772   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
    | 20:10633237   | [108,92]     | [5.40e-01,4.60e-01] |         200 | [31,23]                   |
    | 20:10636995   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
    | 20:10639222   | [175,25]     | [8.75e-01,1.25e-01] |         200 | [78,3]                    |
    | 20:13763601   | [198,2]      | [9.90e-01,1.00e-02] |         200 | [98,0]                    |
    | 20:16223922   | [87,101]     | [4.63e-01,5.37e-01] |         188 | [28,35]                   |
    | 20:17479617   | [191,9]      | [9.55e-01,4.50e-02] |         200 | [91,0]                    |
    +---------------+--------------+---------------------+-------------+---------------------------+
    

Notes

This method is meaningful for computing call metrics per variant, but not
especially meaningful for computing metrics per sample.

This method returns a struct expression with three fields:

>   * AC ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tint32`](types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Allele counts. One element for each allele,
> including the reference.
>
>   * AF ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of
> [`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")) - Allele frequencies. One element for each
> allele, including the reference.
>
>   * AN ([`tint32`](types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Allele number. The total number of called
> alleles, or the number of non-missing calls * 2.
>
>   * homozygote_count ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tint32`](types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Homozygote genotype counts for each allele,
> including the reference. Only **diploid** genotype calls are counted.
>
>

Parameters:

    

  * **call** (```CallExpression```)

  * **alleles** (```ArrayExpression``` of strings or ```Int32Expression```) – Variant alleles array, or number of alleles (including reference).

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields AC, AF, AN, and
homozygote_count.

hail.expr.aggregators.info_score(_gp_)[[source]](_modules/hail/expr/aggregators/aggregators.html#info_score)

    

Compute the IMPUTE information score.

Examples

Calculate the info score per variant:

    
    
    >>> gen_mt = hl.import_gen('data/example.gen', sample_file='data/example.sample')
    >>> gen_mt = gen_mt.annotate_rows(info_score = hl.agg.info_score(gen_mt.GP))
    

Calculate group-specific info scores per variant:

    
    
    >>> gen_mt = hl.import_gen('data/example.gen', sample_file='data/example.sample')
    >>> gen_mt = gen_mt.annotate_cols(is_case = hl.rand_bool(0.5))
    >>> gen_mt = gen_mt.annotate_rows(info_score = hl.agg.group_by(gen_mt.is_case, hl.agg.info_score(gen_mt.GP)))
    

Notes

The result of `info_score()` is a struct with two fields:

>   * score (`float64`) – Info score.
>
>   * n_included (`int32`) – Number of non-missing samples included in the
> calculation.
>
>

We implemented the IMPUTE info measure as described in the supplementary
information from [Marchini & Howie. Genotype imputation for genome-wide
association studies. Nature Reviews Genetics
(2010)](http://www.nature.com/nrg/journal/v11/n7/extref/nrg2796-s3.pdf). To
calculate the info score \\(I_{A}\\) for one SNP:

\\[I_{A} = \begin{cases} 1 - \frac{\sum_{i=1}^{N}(f_{i} -
e_{i}^2)}{2N\hat{\theta}(1 - \hat{\theta})} & \text{when } \hat{\theta} \in
(0, 1) \\\ 1 & \text{when } \hat{\theta} = 0, \hat{\theta} = 1\\\
\end{cases}\\]

  * \\(N\\) is the number of samples with imputed genotype probabilities [\\(p_{ik} = P(G_{i} = k)\\) where \\(k \in \\{0, 1, 2\\}\\)]

  * \\(e_{i} = p_{i1} + 2p_{i2}\\) is the expected genotype per sample

  * \\(f_{i} = p_{i1} + 4p_{i2}\\)

  * \\(\hat{\theta} = \frac{\sum_{i=1}^{N}e_{i}}{2N}\\) is the MLE for the population minor allele frequency

Hail will not generate identical results to
[QCTOOL](http://www.well.ox.ac.uk/~gav/qctool/#overview) for the following
reasons:

  * Hail automatically removes genotype probability distributions that do not meet certain requirements on data import with ```import_gen()``` and ```import_bgen()```.

  * Hail does not use the population frequency to impute genotype probabilities when a genotype probability distribution has been set to missing.

  * Hail calculates the same statistic for sex chromosomes as autosomes while QCTOOL incorporates sex information.

  * The floating point number Hail stores for each genotype probability is slightly different than the original data due to rounding and normalization of probabilities.

Warning

  * The info score Hail reports will be extremely different from QCTOOL when a SNP has a high missing rate.

  * If the gp array must contain 3 elements, and its elements may not be missing.

  * If the genotype data was not imported using the ```import_gen()``` or ```import_bgen()``` functions, then the results for all variants will be `score = NA` and `n_included = 0`.

  * It only makes semantic sense to compute the info score per variant. While the aggregator will run in any context if its arguments are the right type, the results are only meaningful in a narrow context.

Parameters:

    

**gp**
([`ArrayNumericExpression`](hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression")) – Genotype probability array. Must have 3
elements, all of which must be defined.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct with fields score and n_included.

hail.expr.aggregators.hist(_expr_ , _start_ , _end_ ,
_bins_)[[source]](_modules/hail/expr/aggregators/aggregators.html#hist)

    

Compute binned counts of a numeric expression.

Examples

Compute a histogram of field GQ:

    
    
    >>> dataset.aggregate_entries(hl.agg.hist(dataset.GQ, 0, 100, 10))  
    Struct(bin_edges=[0.0, 10.0, 20.0, 30.0, 40.0, 50.0, 60.0, 70.0, 80.0, 90.0, 100.0],
           bin_freq=[2194L, 637L, 2450L, 1081L, 518L, 402L, 11168L, 1918L, 1379L, 11973L]),
           n_smaller=0,
           n_greater=0)
    

Notes

This method returns a struct expression with four fields:

>   * bin_edges ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of
> [`tfloat64`](types.html#hail.expr.types.tfloat64
> "hail.expr.types.tfloat64")): Bin edges. Bin i contains values in the left-
> inclusive, right-exclusive range `[ bin_edges[i], bin_edges[i+1] )`.
>
>   * bin_freq ([`tarray`](types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): Bin frequencies. The number of records found in
> each bin.
>
>   * n_smaller ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): The number of records smaller than the start of
> the first bin.
>
>   * n_larger ([`tint64`](types.html#hail.expr.types.tint64
> "hail.expr.types.tint64")): The number of records larger than the end of the
> last bin.
>
>

Parameters:

    

  * **expr** (```NumericExpression```) – Target numeric expression.

  * **start** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)") or [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Start of histogram range.

  * **end** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)") or [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – End of histogram range.

  * **bins** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of bins.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct expression with fields bin_edges,
bin_freq, n_smaller, and n_larger.

hail.expr.aggregators.linreg(_y_ , _x_ , _nested_dim =1_, _weight
=None_)[[source]](_modules/hail/expr/aggregators/aggregators.html#linreg)

    

Compute multivariate linear regression statistics.

Examples

Regress HT against an intercept (1), SEX, and C1:

    
    
    >>> table1.aggregate(hl.agg.linreg(table1.HT, [1, table1.SEX == 'F', table1.C1]))  
    Struct(beta=[88.50000000000014, 81.50000000000057, -10.000000000000068],
           standard_error=[14.430869689661844, 59.70552738231206, 7.000000000000016],
           t_stat=[6.132686518775844, 1.365032746099571, -1.428571428571435],
           p_value=[0.10290201427537926, 0.40250974549499974, 0.3888002244284281],
           multiple_standard_error=4.949747468305833,
           multiple_r_squared=0.7175792507204611,
           adjusted_r_squared=0.1527377521613834,
           f_stat=1.2704081632653061,
           multiple_p_value=0.5314327326007864,
           n=4)
    

Regress blood pressure against an intercept (1), genotype, age, and the
interaction of genotype and age:

    
    
    >>> ds_ann = ds.annotate_rows(linreg =
    ...     hl.agg.linreg(ds.pheno.blood_pressure,
    ...                   [1,
    ...                    ds.GT.n_alt_alleles(),
    ...                    ds.pheno.age,
    ...                    ds.GT.n_alt_alleles() * ds.pheno.age]))
    

Warning

As in the example, the intercept covariate `1` must be included **explicitly**
if desired.

Notes

In relation to
[lm.summary](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/summary.lm.html)
in R, `linreg(y, x = [1, mt.x1, mt.x2])` computes `summary(lm(y ~ x1 + x2))`
and `linreg(y, x = [mt.x1, mt.x2], nested_dim=0)` computes `summary(lm(y ~ x1
+ x2 - 1))`.

More generally, nested_dim defines the number of effects to fit in the nested
(null) model, with the effects on the remaining covariates fixed to zero.

The returned struct has ten fields:

    

  * beta (```tarray``` of ```tfloat64```): Estimated regression coefficient for each covariate.

  * standard_error (```tarray``` of ```tfloat64```): Estimated standard error for each covariate.

  * t_stat (```tarray``` of ```tfloat64```): t-statistic for each covariate.

  * p_value (```tarray``` of ```tfloat64```): p-value for each covariate.

  * multiple_standard_error (```tfloat64```): Estimated standard deviation of the random error.

  * multiple_r_squared (```tfloat64```): Coefficient of determination for nested models.

  * adjusted_r_squared (```tfloat64```): Adjusted multiple_r_squared taking into account degrees of freedom.

  * f_stat (```tfloat64```): F-statistic for nested models.

  * multiple_p_value (```tfloat64```): p-value for the [F-test](https://en.wikipedia.org/wiki/F-test#Regression_problems) of nested models.

  * n (```tint64```): Number of samples included in the regression. A sample is included if and only if y, all elements of x, and weight (if set) are non-missing.

All but the last field are missing if n is less than or equal to the number of
covariates or if the covariates are linearly dependent.

If set, the weight parameter generalizes the model to [weighted least
squares](https://en.wikipedia.org/wiki/Weighted_least_squares), useful for
heteroscedastic (diagonal but non-constant) variance.

Warning

If any weight is negative, the resulting statistics will be `nan`.

Parameters:

    

  * **y** (```Float64Expression```) – Response (dependent variable).

  * **x** (```Float64Expression``` or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```Float64Expression```) – Covariates (independent variables).

  * **nested_dim** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – The null model includes the first nested_dim covariates. Must be between 0 and k (the length of x).

  * **weight** (```Float64Expression```, optional) – Non-negative weight for weighted least squares.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct of regression results.

hail.expr.aggregators.corr(_x_ ,
_y_)[[source]](_modules/hail/expr/aggregators/aggregators.html#corr)

    

Computes the [Pearson correlation
coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)
between x and y.

Examples

    
    
    >>> ds.aggregate_cols(hl.agg.corr(ds.pheno.age, ds.pheno.blood_pressure))  
    0.16592876044845484
    

Notes

Only records where both x and y are non-missing will be included in the
calculation.

In the case that there are no non-missing pairs, the result will be missing.

See also

`linreg()`

Parameters:

    

  * **x** (```Expression``` of type `tfloat64`)

  * **y** (```Expression``` of type `tfloat64`)

Returns:

    

[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression")

hail.expr.aggregators.group_by(_group_ ,
_agg_expr_)[[source]](_modules/hail/expr/aggregators/aggregators.html#group_by)

    

Compute aggregation statistics stratified by one or more groups.

Examples

Compute linear regression statistics stratified by SEX:

    
    
    >>> table1.aggregate(hl.agg.group_by(table1.SEX,
    ...                                  hl.agg.linreg(table1.HT, table1.C1, nested_dim=0)))  
    {
    'F': Struct(beta=[6.153846153846154],
                standard_error=[0.7692307692307685],
                t_stat=[8.000000000000009],
                p_value=[0.07916684832113098],
                multiple_standard_error=11.4354374979373,
                multiple_r_squared=0.9846153846153847,
                adjusted_r_squared=0.9692307692307693,
                f_stat=64.00000000000014,
                multiple_p_value=0.07916684832113098,
                n=2),
    'M': Struct(beta=[34.25],
                standard_error=[1.75],
                t_stat=[19.571428571428573],
                p_value=[0.03249975499062629],
                multiple_standard_error=4.949747468305833,
                multiple_r_squared=0.9973961101073441,
                adjusted_r_squared=0.9947922202146882,
                f_stat=383.0408163265306,
                multiple_p_value=0.03249975499062629,
                n=2)
    }
    

Compute call statistics stratified by population group and case status:

    
    
    >>> ann = ds.annotate_rows(call_stats=hl.agg.group_by(hl.struct(pop=ds.pop, is_case=ds.is_case),
    ...                                                   hl.agg.call_stats(ds.GT, ds.alleles)))
    

Parameters:

    

  * **group** (```Expression``` or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```Expression```) – Group to stratify the result by.

  * **agg_expr** (```Expression```) – Aggregation or scan expression to compute per grouping.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") – Dictionary where the keys are group and the
values are the result of computing agg_expr for each unique value of group.

hail.expr.aggregators.array_agg(_f_ ,
_array_)[[source]](_modules/hail/expr/aggregators/aggregators.html#array_agg)

    

Aggregate an array element-wise using a user-specified aggregation function.

Examples

Start with a range table with an array of random boolean values:

    
    
    >>> ht = hl.utils.range_table(100)
    >>> ht = ht.annotate(arr = hl.range(0, 5).map(lambda _: hl.rand_bool(0.5)))
    

Aggregate to compute the fraction `True` per element:

    
    
    >>> ht.aggregate(hl.agg.array_agg(lambda element: hl.agg.fraction(element), ht.arr))  
    [0.54, 0.55, 0.46, 0.52, 0.48]
    

Notes

This function requires that all values of array have the same length. If two
values have different lengths, then an exception will be thrown.

The f argument should be a function taking one argument, an expression of the
element type of array, and returning an expression including aggregation(s).
The type of the aggregated expression returned by `array_agg()` is an array of
elements of the return type of f.

Parameters:

    

  * **f** – Aggregation function to apply to each element of the exploded array.

  * **array** (```ArrayExpression```) – Array to aggregate.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.aggregators.downsample(_x_ , _y_ , _label =None_, _n_divisions
=500_)[[source]](_modules/hail/expr/aggregators/aggregators.html#downsample)

    

Downsample (x, y) coordinate datapoints.

Parameters:

    

  * **x** (```NumericExpression```) – X-values to be downsampled.

  * **y** (```NumericExpression```) – Y-values to be downsampled.

  * **label** (```StringExpression``` or ```ArrayExpression```) – Additional data for each (x, y) coordinate. Can pass in multiple fields in an ```ArrayExpression```.

  * **n_divisions** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Expression for downsampled coordinate points
(x, y). The element type of the array is
```ttuple``` of
```tfloat64```,
```tfloat64```,
and ```tarray``` of
```tstr```

hail.expr.aggregators.approx_cdf(_expr_ , _k =100_, _*_ , __raw
=False_)[[source]](_modules/hail/expr/aggregators/aggregators.html#approx_cdf)

    

Produce a summary of the distribution of values.

Notes

This method returns a struct containing two arrays: values and ranks. The
values array contains an ordered sample of values seen. The ranks array is one
longer, and contains the approximate ranks for the corresponding values.

These represent a summary of the CDF of the distribution of values. In
particular, for any value x = values(i) in the summary, we estimate that there
are ranks(i) values strictly less than x, and that there are ranks(i+1) values
less than or equal to x. For any value y (not necessarily in the summary), we
estimate CDF(y) to be ranks(i), where i is such that values(i-1) < y ≤
values(i).

An alternative intuition is that the summary encodes a compressed
approximation to the sorted list of values. For example, values=[0,2,5,6,9]
and ranks=[0,3,4,5,8,10] represents the approximation [0,0,0,2,5,6,6,6,9,9],
with the value values(i) occupying indices ranks(i) (inclusive) to ranks(i+1)
(exclusive).

The returned struct also contains an array _compaction_counts, which is used
internally to support downstream error estimation.

Warning

This is an approximate and nondeterministic method.

Parameters:

    

  * **expr** (```Expression```) – Expression to collect.

  * **k** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Parameter controlling the accuracy vs. memory usage tradeoff.

Returns:

    

[`StructExpression`](hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct containing values and ranks arrays.

---

# Scans

The `scan` module is exposed as `hl.scan`, e.g. `hl.scan.sum`.

The functions in this module perform rolling aggregations along the rows of a
table, or along the rows or columns of a matrix table. The value of the scan
at a given row (or column) is the result of applying the corresponding
aggregator to all previous rows (or columns). Scans directly over entries are
not currently supported.

For example, the `count` aggregator can be used as `hl.scan.count` to add an
index along the rows of a table or the rows or columns of a matrix table; the
two statements below produce identical tables:

    
    
    >>> ht_with_idx = ht.add_index()
    >>> ht_with_idx = ht.annotate(idx=hl.scan.count())
    

For example, to compute a cumulative sum for a row field in a table:

    
    
    >>> ht_scan = ht.select(ht.Z, cum_sum=hl.scan.sum(ht.Z))
    >>> ht_scan.show()
    +-------+-------+---------+
    |    ID |     Z | cum_sum |
    +-------+-------+---------+
    | int32 | int32 |   int64 |
    +-------+-------+---------+
    |     1 |     4 |       0 |
    |     2 |     3 |       4 |
    |     3 |     3 |       7 |
    |     4 |     2 |      10 |
    +-------+-------+---------+
    

Note that the cumulative sum is exclusive of the current row’s value. On a
matrix table, to compute the cumulative number of non-reference genotype calls
along the genome:

    
    
    >>> ds_scan = ds.select_rows(ds.variant_qc.n_non_ref,
    ...                          cum_n_non_ref=hl.scan.sum(ds.variant_qc.n_non_ref))
    >>> ds_scan.rows().show()
    +---------------+------------+-----------+---------------+
    | locus         | alleles    | n_non_ref | cum_n_non_ref |
    +---------------+------------+-----------+---------------+
    | locus<GRCh37> | array<str> |     int64 |         int64 |
    +---------------+------------+-----------+---------------+
    | 20:10579373   | ["C","T"]  |         1 |             0 |
    | 20:10579398   | ["C","T"]  |         1 |             1 |
    | 20:10627772   | ["C","T"]  |         2 |             2 |
    | 20:10633237   | ["G","A"]  |        69 |             4 |
    | 20:10636995   | ["C","T"]  |         2 |            73 |
    | 20:10639222   | ["G","A"]  |        22 |            75 |
    | 20:13763601   | ["A","G"]  |         2 |            97 |
    | 20:16223922   | ["T","C"]  |        66 |            99 |
    | 20:17479617   | ["G","A"]  |         9 |           165 |
    +---------------+------------+-----------+---------------+
    

Scans over column fields can be done in a similar manner.

Danger

Computing the result of certain aggregators, such as
[`hardy_weinberg_test()`](aggregators.html#hail.expr.aggregators.hardy_weinberg_test
"hail.expr.aggregators.hardy_weinberg_test"), can be very expensive when done
for every row in a scan.”

See the ``Aggregators`` module for
documentation on the behavior of specific aggregators.

---

# Methods

  * ``Import / Export``
    * ``Native file formats``
    * ``Import``
    * ``Export``
    * ``Reference documentation``
  * ``Statistics``
    * ```linear_mixed_model()```
    * ```linear_mixed_regression_rows()```
    * ```linear_regression_rows()```
    * ```logistic_regression_rows()```
    * ```poisson_regression_rows()```
    * ```pca()```
    * ```row_correlation()```
  * ``Genetics``
    * ```VEPConfig```
    * ```VEPConfigGRCh37Version85```
    * ```VEPConfigGRCh38Version95```
    * ```balding_nichols_model()```
    * ```concordance()```
    * ```filter_intervals()```
    * ```filter_alleles()```
    * ```filter_alleles_hts()```
    * ```hwe_normalized_pca()```
    * ```genetic_relatedness_matrix()```
    * ```realized_relationship_matrix()```
    * ```impute_sex()```
    * ```ld_matrix()```
    * ```ld_prune()```
    * ```compute_charr()```
    * ```mendel_errors()```
    * ```de_novo()```
    * ```nirvana()```
    * ```sample_qc()```
    * ```_logistic_skat()```
    * ```skat()```
    * ```lambda_gc()```
    * ```split_multi()```
    * ```split_multi_hts()```
    * ```summarize_variants()```
    * ```transmission_disequilibrium_test()```
    * ```trio_matrix()```
    * ```variant_qc()```
    * ```vep()```
  * ``Relatedness``
    * ```identity_by_descent()```
    * ```king()```
    * ```pc_relate()```
    * ```simulate_random_mating()```
  * ``Miscellaneous``
    * ```grep()```
    * ```maximal_independent_set()```
    * ```rename_duplicates()```
    * ```segment_intervals()```

Import / Export

```export_elasticsearch```(t, host, port, index, ...) | Export a ```Table``` to Elasticsearch.  
---|---  
```export_gen```(dataset, output[, precision, gp, ...]) | Export a ```MatrixTable``` as GEN and SAMPLE files.  
```export_bgen```(mt, output[, gp, varid, rsid, ...]) | Export MatrixTable as ```MatrixTable``` as BGEN 1.2 file with 8 bits of per probability.  
```export_plink```(dataset, output[, call, ...]) | Export a ```MatrixTable``` as [PLINK2](https://www.cog-genomics.org/plink2/formats) BED, BIM and FAM files.  
```export_vcf```(dataset, output[, ...]) | Export a ```MatrixTable``` or ```Table``` as a VCF file.  
```get_vcf_metadata```(path) | Extract metadata from VCF header.  
```import_bed```(path[, reference_genome, ...]) | Import a UCSC BED file as a ```Table```.  
```import_bgen```(path, entry_fields[, ...]) | Import BGEN file(s) as a ```MatrixTable```.  
```import_fam```(path[, quant_pheno, delimiter, ...]) | Import a PLINK FAM file into a ```Table```.  
```import_gen```(path[, sample_file, tolerance, ...]) | Import GEN file(s) as a ```MatrixTable```.  
```import_locus_intervals```(path[, ...]) | Import a locus interval list as a ```Table```.  
```import_matrix_table```(paths[, row_fields, ...]) | Import tab-delimited file(s) as a ```MatrixTable```.  
```import_plink```(bed, bim, fam[, ...]) | Import a PLINK dataset (BED, BIM, FAM) as a ```MatrixTable```.  
```import_table```(paths[, key, min_partitions, ...]) | Import delimited text file (text table) as ```Table```.  
```import_vcf```(path[, force, force_bgz, ...]) | Import VCF file(s) as a ```MatrixTable```.  
```index_bgen```(path[, index_file_map, ...]) | Index BGEN files as required by ```import_bgen()```.  
```read_matrix_table```(path, *[, _intervals, ...]) | Read in a ```MatrixTable``` written with ```MatrixTable.write()```.  
```read_table```(path, *[, _intervals, ...]) | Read in a ```Table``` written with ```Table.write()```.  
  
Statistics

```linear_mixed_model```(y, x[, z_t, k, p_path, ...]) | Initialize a linear mixed model from a matrix table.  
---|---  
```linear_mixed_regression_rows```(entry_expr, model) | For each row, test an input variable for association using a linear mixed model.  
```linear_regression_rows```(y, x, covariates[, ...]) | For each row, test an input variable for association with response variables using linear regression.  
```logistic_regression_rows```(test, y, x, covariates) | For each row, test an input variable for association with a binary response variable using logistic regression.  
```poisson_regression_rows```(test, y, x, covariates) | For each row, test an input variable for association with a count response variable using [Poisson regression](https://en.wikipedia.org/wiki/Poisson_regression).  
```pca```(entry_expr[, k, compute_loadings]) | Run principal component analysis (PCA) on numeric columns derived from a matrix table.  
```row_correlation```(entry_expr[, block_size]) | Computes the correlation matrix between row vectors.  
  
Genetics

```balding_nichols_model```(n_populations, ...[, ...]) | Generate a matrix table of variants, samples, and genotypes using the Balding-Nichols or Pritchard-Stephens-Donnelly model.  
---|---  
```concordance```(left, right, *[, ...]) | Calculate call concordance with another dataset.  
```filter_intervals```(ds, intervals[, keep]) | Filter rows with a list of intervals.  
```filter_alleles```(mt, f) | Filter alternate alleles.  
```filter_alleles_hts```(mt, f[, subset]) | Filter alternate alleles and update standard GATK entry fields.  
```genetic_relatedness_matrix```(call_expr) | Compute the genetic relatedness matrix (GRM).  
```hwe_normalized_pca```(call_expr[, k, ...]) | Run principal component analysis (PCA) on the Hardy-Weinberg-normalized genotype call matrix.  
```impute_sex```(call[, aaf_threshold, ...]) | Impute sex of samples by calculating inbreeding coefficient on the X chromosome.  
```ld_matrix```(entry_expr, locus_expr, radius[, ...]) | Computes the windowed correlation (linkage disequilibrium) matrix between variants.  
```ld_prune```(call_expr[, r2, bp_window_size, ...]) | Returns a maximal subset of variants that are nearly uncorrelated within each window.  
```compute_charr```(ds[, min_af, max_af, min_dp, ...]) | Compute CHARR, the DNA sample contamination estimator.  
```mendel_errors```(call, pedigree) | Find Mendel errors; count per variant, individual and nuclear family.  
```de_novo```(mt, pedigree, pop_frequency_prior, *) | Call putative _de novo_ events from trio data.  
```nirvana```(dataset, config[, block_size, name]) | Annotate variants using [Nirvana](https://github.com/Illumina/Nirvana).  
```realized_relationship_matrix```(call_expr) | Computes the realized relationship matrix (RRM).  
```sample_qc```(mt[, name]) | Compute per-sample metrics useful for quality control.  
```skat```(key_expr, weight_expr, y, x, covariates) | Test each keyed group of rows for association by linear or logistic SKAT test.  
```lambda_gc```(p_value[, approximate]) | Compute genomic inflation factor (lambda GC) from an Expression of p-values.  
```split_multi```(ds[, keep_star, left_aligned, ...]) | Split multiallelic variants.  
```split_multi_hts```(ds[, keep_star, ...]) | Split multiallelic variants for datasets that contain one or more fields from a standard high-throughput sequencing entry schema.  
```transmission_disequilibrium_test```(dataset, ...) | Performs the transmission disequilibrium test on trios.  
```trio_matrix```(dataset, pedigree[, complete_trios]) | Builds and returns a matrix where columns correspond to trios and entries contain genotypes for the trio.  
```variant_qc```(mt[, name]) | Compute common variant statistics (quality control metrics).  
```vep```(dataset[, config, block_size, name, ...]) | Annotate variants with VEP.  
  
Relatedness

Hail provides three methods for the inference of relatedness: PLINK-style
identity by descent [1], KING [2], and PC-Relate [3].

  * ```identity_by_descent()``` is appropriate for datasets containing one homogeneous population.

  * ```king()``` is appropriate for datasets containing multiple homogeneous populations and no admixture. It is also used to prune close relatives before using ```pc_relate()```.

  * ```pc_relate()``` is appropriate for datasets containing multiple homogeneous populations and admixture.

```identity_by_descent```(dataset[, maf, bounded, ...]) | Compute matrix of identity-by-descent estimates.  
---|---  
```king```(call_expr, *[, block_size]) | Compute relatedness estimates between individuals using a KING variant.  
```pc_relate```(call_expr, min_individual_maf, *) | Compute relatedness estimates between individuals using a variant of the PC-Relate method.  
  
Miscellaneous

```grep```(regex, path[, max_count, show, force, ...]) | Searches given paths for all lines containing regex matches.  
---|---  
```maximal_independent_set```(i, j[, keep, ...]) | Return a table containing the vertices in a near [maximal independent set](https://en.wikipedia.org/wiki/Maximal_independent_set) of an undirected graph whose edges are given by a two-column table.  
```rename_duplicates```(dataset[, name]) | Rename duplicate column keys.  
```segment_intervals```(ht, points) | Segment the interval keys of ht at a given set of points.  
[1]

Purcell, Shaun et al. “PLINK: a tool set for whole-genome association and
population-based linkage analyses.” American journal of human genetics vol.
81,3 (2007): 559-75. doi:10.1086/519795.
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC1950838/

[2]

Manichaikul, Ani et al. “Robust relationship inference in genome-wide
association studies.” Bioinformatics (Oxford, England) vol. 26,22 (2010):
2867-73. doi:10.1093/bioinformatics/btq559.
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3025716/

[3]

Conomos, Matthew P et al. “Model-free Estimation of Recent Genetic
Relatedness.” American journal of human genetics vol. 98,1 (2016): 127-48.
doi:10.1016/j.ajhg.2015.11.022.
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4716688/

---

# nd

NDArray Functions

## Notes

This is a recently added, experimental module. We would love to hear what use
cases you have for this as we expand this functionality. As much as possible,
we try to mimic the numpy array interface.

`array`(input_array[, dtype]) | Construct an ```NDArrayExpression```  
---|---  
`arange`(start[, stop, step]) | Returns a 1-dimensions ndarray of integers from start to stop by step.  
`full`(shape, value[, dtype]) | Creates a hail ```NDArrayNumericExpression``` full of the specified value.  
`zeros`(shape[, dtype]) | Creates a hail ```NDArrayNumericExpression``` full of zeros.  
`ones`(shape[, dtype]) | Creates a hail ```NDArrayNumericExpression``` full of ones.  
`diagonal`(nd) | Gets the diagonal of a 2 dimensional NDArray.  
`solve`(a, b[, no_crash]) | Solve a linear system.  
`solve_triangular`(A, b[, lower, no_crash]) | Solve a triangular linear system Ax = b for x.  
`qr`(nd[, mode]) | Performs a QR decomposition.  
`svd`(nd[, full_matrices, compute_uv]) | Performs a singular value decomposition.  
`inv`(nd) | Performs a matrix inversion.  
`concatenate`(nds[, axis]) | Join a sequence of arrays along an existing axis.  
`hstack`(arrs) | Stack arrays in sequence horizontally (column wise).  
`vstack`(arrs) | Stack arrays in sequence vertically (row wise).  
`eye`(N[, M, dtype]) | Construct a 2-D ```NDArrayExpression``` with ones on the _main_ diagonal and zeros elsewhere.  
`identity`(N[, dtype]) | Constructs a 2-D ```NDArrayExpression``` representing the identity array.  
`maximum`(nd1, nd2) | Compares elements at corresponding indexes in arrays and returns an array of the maximum element found at each compared index.  
`minimum`(nd1, nd2) | Compares elements at corresponding indexes in arrays and returns an array of the minimum element found at each compared index.  
  
hail.nd.array(_input_array_ , _dtype
=None_)[[source]](../_modules/hail/nd/nd.html#array)

    

Construct an
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")

Examples

    
    
    >>> hl.eval(hl.nd.array([1, 2, 3, 4]))
    array([1, 2, 3, 4], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.array([[1, 2, 3], [4, 5, 6]]))
    array([[1, 2, 3],
       [4, 5, 6]], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.array(np.identity(3)))
    array([[1., 0., 0.],
       [0., 1., 0.],
       [0., 0., 1.]])
    
    
    
    >>> hl.eval(hl.nd.array(hl.range(10, 20)))
    array([10, 11, 12, 13, 14, 15, 16, 17, 18, 19], dtype=int32)
    

Parameters:

    

  * **input_array** (```ArrayExpression```, numpy ndarray, or nested python lists/tuples) – The array to convert to a Hail ndarray.

  * **dtype** (```HailType```) – Desired hail type. Default: float64.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – An ndarray based on the input array.

hail.nd.arange(_start_ , _stop =None_, _step
=1_)[[source]](../_modules/hail/nd/nd.html#arange)

    

Returns a 1-dimensions ndarray of integers from start to stop by step.

Examples

    
    
    >>> hl.eval(hl.nd.arange(10))
    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.arange(3, 10))
    array([3, 4, 5, 6, 7, 8, 9], dtype=int32)
    
    
    
    >>> hl.eval(hl.nd.arange(0, 10, step=3))
    array([0, 3, 6, 9], dtype=int32)
    

Notes

The range includes start, but excludes stop.

If provided exactly one argument, the argument is interpreted as stop and
start is set to zero. This matches the behavior of Python’s `range`.

Parameters:

    

  * **start** (int or ```Expression``` of type ```tint32```) – Start of range.

  * **stop** (int or ```Expression``` of type ```tint32```) – End of range.

  * **step** (int or ```Expression``` of type ```tint32```) – Step of range.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – A 1-dimensional ndarray from start to
stop by step.

hail.nd.full(_shape_ , _value_ , _dtype
=None_)[[source]](../_modules/hail/nd/nd.html#full)

    

Creates a hail
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") full of the specified value.

Examples

Create a 5 by 7 NDArray of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") 9s.

    
    
    >>> hl.nd.full((5, 7), 9)
    

It is possible to specify a type other than
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") with the dtype argument.

    
    
    >>> hl.nd.full((5, 7), 9, dtype=hl.tint32)
    

Parameters:

    

  * **shape** (tuple or ```TupleExpression```) – Desired shape.

  * **value** (```Expression``` or python value) – Value to fill ndarray with.

  * **dtype** (```HailType```) – Desired hail type.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – An ndarray of the specified shape
filled with the specified value.

hail.nd.zeros(_shape_ , _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#zeros)

    

Creates a hail
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") full of zeros.

Examples

Create a 5 by 7 NDArray of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") zeros.

    
    
    >>> hl.nd.zeros((5, 7))
    

It is possible to specify a type other than
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") with the dtype argument.

    
    
    >>> hl.nd.zeros((5, 7), dtype=hl.tfloat32)
    

Parameters:

    

  * **shape** (tuple or ```TupleExpression```) – Desired shape.

  * **dtype** (```HailType```) – Desired hail type. Default: float64.

See also

`full()`

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – ndarray of the specified size full of
zeros.

hail.nd.ones(_shape_ , _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#ones)

    

Creates a hail
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") full of ones.

Examples

Create a 5 by 7 NDArray of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") ones.

    
    
    >>> hl.nd.ones((5, 7))
    

It is possible to specify a type other than
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") with the dtype argument.

    
    
    >>> hl.nd.ones((5, 7), dtype=hl.tfloat32)
    

Parameters:

    

  * **shape** (tuple or ```TupleExpression```) – Desired shape.

  * **dtype** (```HailType```) – Desired hail type. Default: float64.

See also

`full()`

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – ndarray of the specified size full of
ones.

hail.nd.diagonal(_nd_)[[source]](../_modules/hail/nd/nd.html#diagonal)

    

Gets the diagonal of a 2 dimensional NDArray.

Examples

    
    
    >>> hl.eval(hl.nd.diagonal(hl.nd.array([[1, 2], [3, 4]])))
    array([1, 4], dtype=int32)
    

Parameters:

    

**nd**
([`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression")) – A 2 dimensional NDArray, shape(M, N).

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – A 1 dimension NDArray of length min(M, N),
containing the diagonal of nd.

hail.nd.solve(_a_ , _b_ , _no_crash
=False_)[[source]](../_modules/hail/nd/nd.html#solve)

    

Solve a linear system.

Parameters:

    

  * **a** (```NDArrayNumericExpression```, (N, N)) – Coefficient matrix.

  * **b** (```NDArrayNumericExpression```, (N,) or (N, K)) – Dependent variables.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"), (N,) or (N, K) – Solution to the system
Ax = B. Shape is same as shape of B.

hail.nd.solve_triangular(_A_ , _b_ , _lower =False_, _no_crash
=False_)[[source]](../_modules/hail/nd/nd.html#solve_triangular)

    

Solve a triangular linear system Ax = b for x.

Parameters:

    

  * **A** (```NDArrayNumericExpression```, (N, N)) – Triangular coefficient matrix.

  * **b** (```NDArrayNumericExpression```, (N,) or (N, K)) – Dependent variables.

  * **lower** (bool:) – If true, A is interpreted as a lower triangular matrix If false, A is interpreted as a upper triangular matrix

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"), (N,) or (N, K) – Solution to the
triangular system Ax = B. Shape is same as shape of B.

hail.nd.qr(_nd_ , _mode
='reduced'_)[[source]](../_modules/hail/nd/nd.html#qr)

    

Performs a QR decomposition.

If K = min(M, N), then:

  * reduced: returns q and r with dimensions (M, K), (K, N)

  * complete: returns q and r with dimensions (M, M), (M, N)

  * r: returns only r with dimensions (K, N)

  * raw: returns h, tau with dimensions (N, M), (K,)

Notes

The reduced QR, the default output of this function, has the following
properties:

\\[m \ge n \\\ nd : \mathbb{R}^{m \times n} \\\ Q : \mathbb{R}^{m \times n}
\\\ R : \mathbb{R}^{n \times n} \\\ \\\ Q^T Q = \mathbb{1}\\]

The complete QR, has the following properties:

\\[m \ge n \\\ nd : \mathbb{R}^{m \times n} \\\ Q : \mathbb{R}^{m \times m}
\\\ R : \mathbb{R}^{m \times n} \\\ \\\ Q^T Q = \mathbb{1} Q Q^T =
\mathbb{1}\\]

Parameters:

    

  * **nd** (```NDArrayExpression```) – A 2 dimensional ndarray, shape(M, N)

  * **mode** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – One of “reduced”, “complete”, “r”, or “raw”. Defaults to “reduced”.

Returns:

    

  * **\- q** (_ndarray of float64_) – A matrix with orthonormal columns.

  * **\- r** (_ndarray of float64_) – The upper-triangular matrix R.

  * **\- (h, tau)** (_ndarrays of float64_) – The array h contains the Householder reflectors that generate q along with r. The tau array contains scaling factors for the reflectors

hail.nd.svd(_nd_ , _full_matrices =True_, _compute_uv
=True_)[[source]](../_modules/hail/nd/nd.html#svd)

    

Performs a singular value decomposition.

Parameters:

    

  * **nd** (```NDArrayNumericExpression```) – A 2 dimensional ndarray, shape(M, N).

  * **full_matrices** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If True (default), u and vt have dimensions (M, M) and (N, N) respectively. Otherwise, they have dimensions (M, K) and (K, N), where K = min(M, N)

  * **compute_uv** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If True (default), compute the singular vectors u and v. Otherwise, only return a single ndarray, s.

Returns:

    

  * **\- u** (```NDArrayNumericExpression```) – The left singular vectors.

  * **\- s** (```NDArrayNumericExpression```) – The singular values.

  * **\- vt** (```NDArrayNumericExpression```) – The right singular vectors.

hail.nd.inv(_nd_)[[source]](../_modules/hail/nd/nd.html#inv)

    

Performs a matrix inversion.

Parameters:

    

**nd**
([`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression")) – A 2 dimensional ndarray.

Returns:

    

[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression") – The inverted matrix.

hail.nd.concatenate(_nds_ , _axis
=0_)[[source]](../_modules/hail/nd/nd.html#concatenate)

    

Join a sequence of arrays along an existing axis.

Examples

    
    
    >>> x = hl.nd.array([[1., 2.], [3., 4.]])
    >>> y = hl.nd.array([[5.], [6.]])
    >>> hl.eval(hl.nd.concatenate([x, y], axis=1))
    array([[1., 2., 5.],
           [3., 4., 6.]])
    >>> x = hl.nd.array([1., 2.])
    >>> y = hl.nd.array([3., 4.])
    >>> hl.eval(hl.nd.concatenate((x, y), axis=0))
    array([1., 2., 3., 4.])
    

Parameters:

    

  * **nds** (a sequence of ```NDArrayNumericExpression```) – The arrays must have the same shape, except in the dimension corresponding to axis (the first, by default). Note: unlike Numpy, the numerical element type of each array_like must match.

  * **axis** (_int, optional_) – The axis along which the arrays will be joined. Default is 0. Note: unlike Numpy, if provided, axis cannot be None.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – The concatenated array

hail.nd.hstack(_arrs_)[[source]](../_modules/hail/nd/nd.html#hstack)

    

Stack arrays in sequence horizontally (column wise). Equivalent to
concatenation along the second axis, except for 1-D arrays where it
concatenates along the first axis.

This function makes most sense for arrays with up to 3 dimensions.
`concatenate()` provides more general stacking and concatenation operations.

Parameters:

    

**tup** (sequence of
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")) – The arrays must have the same shape along
all but the second axis, except 1-D arrays which can be any length.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – The array formed by stacking the given
arrays.

See also

`concatenate()`, `vstack()`

Examples

    
    
    >>> a = hl.nd.array([1,2,3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.hstack((a,b)))
    array([1, 2, 3, 2, 3, 4], dtype=int32)
    >>> a = hl.nd.array([[1],[2],[3]])
    >>> b = hl.nd.array([[2],[3],[4]])
    >>> hl.eval(hl.nd.hstack((a,b)))
    array([[1, 2],
           [2, 3],
           [3, 4]], dtype=int32)
    

hail.nd.vstack(_arrs_)[[source]](../_modules/hail/nd/nd.html#vstack)

    

Stack arrays in sequence vertically (row wise). 1-D arrays of shape (N,), will
reshaped to (1,N) before concatenation. For all other arrays, equivalent to
`concatenate()` with axis=0.

Examples

    
    
    >>> a = hl.nd.array([1, 2, 3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.vstack((a,b)))
    array([[1, 2, 3],
           [2, 3, 4]], dtype=int32)
    >>> a = hl.nd.array([[1], [2], [3]])
    >>> b = hl.nd.array([[2], [3], [4]])
    >>> hl.eval(hl.nd.vstack((a,b)))
    array([[1],
           [2],
           [3],
           [2],
           [3],
           [4]], dtype=int32)
    

Parameters:

    

**arrs** (sequence of
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")) – The arrays must have the same shape along
all but the first axis. 1-D arrays must have the same length.

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – The array formed by stacking the given
arrays, will be at least 2-D.

See also

`concatenate()`

    

Join a sequence of arrays along an existing axis.

hail.nd.eye(_N_ , _M =None_, _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#eye)

    

Construct a 2-D
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") with ones on the _main_ diagonal and zeros
elsewhere.

Examples

    
    
    >>> hl.eval(hl.nd.eye(3))
    array([[1., 0., 0.],
           [0., 1., 0.],
           [0., 0., 1.]])
    >>> hl.eval(hl.nd.eye(2, 5, dtype=hl.tint32))
    array([[1, 0, 0, 0, 0],
           [0, 1, 0, 0, 0]], dtype=int32)
    

Parameters:

    

  * **N** (```NumericExpression``` or Python number) – Number of rows in the output.

  * **M** (```NumericExpression``` or Python number, optional) – Number of columns in the output. If None, defaults to N.

  * **dtype** (numeric ```HailType```, optional) – Element type of the returned array. Defaults to ```tfloat64```

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – An (N, M) matrix with ones on the main
diagonal, zeros elsewhere.

See also

`identity()`, `diagonal()`

hail.nd.identity(_N_ , _dtype
=dtype('float64')_)[[source]](../_modules/hail/nd/nd.html#identity)

    

Constructs a 2-D
[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") representing the identity array. The identity
array is a square array with ones on the main diagonal.

Examples

    
    
    >>> hl.eval(hl.nd.identity(3))
    array([[1., 0., 0.],
           [0., 1., 0.],
           [0., 0., 1.]])
    

Parameters:

    

  * **n** (```NumericExpression``` or Python number) – Number of rows and columns in the output.

  * **dtype** (numeric ```HailType```, optional) – Element type of the returned array. Defaults to ```tfloat64```

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – An (N, N) matrix with its main diagonal set
to one, and all other elements 0.

See also

`eye()`

hail.nd.maximum(_nd1_ , _nd2_)[[source]](../_modules/hail/nd/nd.html#maximum)

    

Compares elements at corresponding indexes in arrays and returns an array of
the maximum element found at each compared index.

If an array element being compared has the value NaN, the maximum for that
index will be NaN.

Examples

    
    
    >>> a = hl.nd.array([1, 5, 3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.maximum(a, b))
    array([2, 5, 4], dtype=int32)
    >>> a = hl.nd.array([hl.float64(float("NaN")), 5.0, 3.0])
    >>> b = hl.nd.array([2.0, 3.0, hl.float64(float("NaN"))])
    >>> hl.eval(hl.nd.maximum(a, b))
    array([nan, 5., nan])
    

Parameters:

    

  * **nd1** (```NDArrayExpression```)

  * **nd2** (class:.NDArrayExpression, .ArrayExpression, numpy ndarray, or nested python lists/tuples.) – Nd1 and nd2 must be the same shape or broadcastable into common shape. Nd1 and nd2 must have elements of comparable types

Returns:

    

[`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression") – Element-wise maximums of nd1 and nd2. If nd1
has the same shape as nd2, the resulting array will be of that shape. If nd1
and nd2 were broadcasted into a common shape, the resulting array will be of
that shape

hail.nd.minimum(_nd1_ , _nd2_)[[source]](../_modules/hail/nd/nd.html#minimum)

    

Compares elements at corresponding indexes in arrays and returns an array of
the minimum element found at each compared index.

If an array element being compared has the value NaN, the minimum for that
index will be NaN.

Examples

    
    
    >>> a = hl.nd.array([1, 5, 3])
    >>> b = hl.nd.array([2, 3, 4])
    >>> hl.eval(hl.nd.minimum(a, b))
    array([1, 3, 3], dtype=int32)
    >>> a = hl.nd.array([hl.float64(float("NaN")), 5.0, 3.0])
    >>> b = hl.nd.array([2.0, 3.0, hl.float64(float("NaN"))])
    >>> hl.eval(hl.nd.minimum(a, b))
    array([nan, 3., nan])
    

Parameters:

    

  * **nd1** (```NDArrayExpression```)

  * **nd2** (class:.NDArrayExpression, .ArrayExpression, numpy ndarray, or nested python lists/tuples.) – nd1 and nd2 must be the same shape or broadcastable into common shape. Nd1 and nd2 must have elements of comparable types

Returns:

    

**min_array**
([`NDArrayExpression`](../hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression")) – Element-wise minimums of nd1 and nd2. If nd1
has the same shape as nd2, the resulting array will be of that shape. If nd1
and nd2 were broadcasted into a common shape, resulting array will be of that
shape

---

# utils

`ANY_REGION` | Built-in mutable sequence.  
---|---  
`Interval`(start, end[, includes_start, ...]) | An object representing a range of values between start and end.  
`Struct`(**kwargs) | Nested annotation structure.  
`frozendict`(d) | An object representing an immutable dictionary.  
`hadoop_open`(path[, mode, buffer_size]) | Open a file through the Hadoop filesystem API.  
`hadoop_copy`(src, dest) | Copy a file through the Hadoop filesystem API.  
`hadoop_exists`(path) | Returns `True` if path exists.  
`hadoop_is_file`(path) | Returns `True` if path both exists and is a file.  
`hadoop_is_dir`(path) | Returns `True` if path both exists and is a directory.  
`hadoop_stat`(path) | Returns information about the file or directory at a given path.  
`hadoop_ls`(path) | Returns information about files at path.  
`hadoop_scheme_supported`(scheme) | Returns `True` if the Hadoop filesystem supports URLs with the given scheme.  
`copy_log`(path) | Attempt to copy the session log to a hadoop-API-compatible location.  
`range_table`(n[, n_partitions]) | Construct a table with the row index and no other fields.  
`range_matrix_table`(n_rows, n_cols[, ...]) | Construct a matrix table with row and column indices and no entry fields.  
`get_1kg`(output_dir[, overwrite]) | Download subset of the [1000 Genomes](http://www.internationalgenome.org/) dataset and sample annotations.  
`get_hgdp`(output_dir[, overwrite]) | Download subset of the [Human Genome Diversity Panel](https://www.internationalgenome.org/data-portal/data-collection/hgdp/) dataset and sample annotations.  
`get_movie_lens`(output_dir[, overwrite]) | Download public Movie Lens dataset.  
  
_class _hail.utils.Interval(_start_ , _end_ , _includes_start =True_,
_includes_end =False_, _point_type
=None_)[[source]](../_modules/hail/utils/interval.html#Interval)

    

An object representing a range of values between start and end.

    
    
    >>> interval2 = hl.Interval(3, 6)
    

Parameters:

    

  * **start** (_any type_) – Object with type point_type.

  * **end** (_any type_) – Object with type point_type.

  * **includes_start** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Interval includes start.

  * **includes_end** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Interval includes end.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.interval.take(5)`. This is rare; it is much more common
to manipulate the
[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression") object, which is constructed using the
following functions:

>   *
> [`interval()`](../functions/constructors.html#hail.expr.functions.interval
> "hail.expr.functions.interval")
>
>   *
> [`locus_interval()`](../functions/genetics.html#hail.expr.functions.locus_interval
> "hail.expr.functions.locus_interval")
>
>   *
> [`parse_locus_interval()`](../functions/genetics.html#hail.expr.functions.parse_locus_interval
> "hail.expr.functions.parse_locus_interval")
>
>

_class _hail.utils.Struct(_**
kwargs_)[[source]](../_modules/hail/utils/struct.html#Struct)

    

Nested annotation structure.

    
    
    >>> bar = hl.Struct(**{'foo': 5, '1kg': 10})
    

Struct elements are treated as both ‘items’ and ‘attributes’, which allows
either syntax for accessing the element “foo” of struct “bar”:

    
    
    >>> bar.foo
    >>> bar['foo']
    

Field names that are not valid Python identifiers, such as fields that start
with numbers or contain spaces, must be accessed with the latter syntax:

    
    
    >>> bar['1kg']
    

The `pprint` module can be used to print nested Structs in a more human-
readable fashion:

    
    
    >>> from pprint import pprint
    >>> pprint(bar)
    

Parameters:

    

**attributes** – Field names and values.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.info.take(5)`. This is rare; it is much more common to
manipulate the
[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") object, which is constructed using the
[`struct()`](../functions/constructors.html#hail.expr.functions.struct
"hail.expr.functions.struct") function.

_class
_hail.utils.frozendict(_d_)[[source]](../_modules/hailtop/frozendict.html#frozendict)

    

An object representing an immutable dictionary.

    
    
    >>> my_frozen_dict = hl.utils.frozendict({1:2, 7:5})
    

To get a normal python dictionary with the same elements from a frozendict:

    
    
    >>> dict(frozendict({'a': 1, 'b': 2}))
    

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.my_dict.take(5)`. This is rare; it is much more common
to manipulate the
[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") object, which is constructed using
[`dict()`](../functions/constructors.html#hail.expr.functions.dict
"hail.expr.functions.dict"). This class is necessary because hail supports
using dicts as keys to other dicts or as elements in sets, while python does
not.

hail.utils.hadoop_open(_path_ , _mode ='r'_, _buffer_size
=8192_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_open)

    

Open a file through the Hadoop filesystem API. Supports distributed file
systems like hdfs, gs, and s3.

Warning

Due to an implementation limitation, `hadoop_open()` may be quite slow for
large data sets (anything larger than 50 MB).

Examples

Write a Pandas DataFrame as a CSV directly into Google Cloud Storage:

    
    
    >>> with hadoop_open('gs://my-bucket/df.csv', 'w') as f: 
    ...     pandas_df.to_csv(f)
    

Read and print the lines of a text file stored in Google Cloud Storage:

    
    
    >>> with hadoop_open('gs://my-bucket/notes.txt') as f: 
    ...     for line in f:
    ...         print(line.strip())
    

Write two lines directly to a file in Google Cloud Storage:

    
    
    >>> with hadoop_open('gs://my-bucket/notes.txt', 'w') as f: 
    ...     f.write('result1: %s\n' % result1)
    ...     f.write('result2: %s\n' % result2)
    

Unpack a packed Python struct directly from a file in Google Cloud Storage:

    
    
    >>> from struct import unpack
    >>> with hadoop_open('gs://my-bucket/notes.txt', 'rb') as f: 
    ...     print(unpack('<f', bytearray(f.read())))
    

Notes

The supported modes are:

>   * `'r'` – Readable text file
> ([`io.TextIOWrapper`](https://docs.python.org/3.9/library/io.html#io.TextIOWrapper
> "\(in Python v3.9\)")). Default behavior.
>
>   * `'w'` – Writable text file
> ([`io.TextIOWrapper`](https://docs.python.org/3.9/library/io.html#io.TextIOWrapper
> "\(in Python v3.9\)")).
>
>   * `'x'` – Exclusive writable text file
> ([`io.TextIOWrapper`](https://docs.python.org/3.9/library/io.html#io.TextIOWrapper
> "\(in Python v3.9\)")). Throws an error if a file already exists at the
> path.
>
>   * `'rb'` – Readable binary file
> ([`io.BufferedReader`](https://docs.python.org/3.9/library/io.html#io.BufferedReader
> "\(in Python v3.9\)")).
>
>   * `'wb'` – Writable binary file
> ([`io.BufferedWriter`](https://docs.python.org/3.9/library/io.html#io.BufferedWriter
> "\(in Python v3.9\)")).
>
>   * `'xb'` – Exclusive writable binary file
> ([`io.BufferedWriter`](https://docs.python.org/3.9/library/io.html#io.BufferedWriter
> "\(in Python v3.9\)")). Throws an error if a file already exists at the
> path.
>
>

The provided destination file path must be a URI (uniform resource
identifier).

Caution

These file handles are slower than standard Python file handles. If you are
writing a large file (larger than ~50M), it will be faster to write to a local
file using standard Python I/O and use `hadoop_copy()` to move your file to a
distributed file system.

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to file.

  * **mode** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – File access mode.

  * **buffer_size** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Buffer size, in bytes.

Returns:

    

_Readable or writable file handle._

hail.utils.hadoop_copy(_src_ ,
_dest_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_copy)

    

Copy a file through the Hadoop filesystem API. Supports distributed file
systems like hdfs, gs, and s3.

Examples

Copy a file from Google Cloud Storage to a local file:

    
    
    >>> hadoop_copy('gs://hail-common/LCR.interval_list',
    ...             'file:///mnt/data/LCR.interval_list') 
    

Notes

Try using `hadoop_open()` first, it’s simpler, but not great for large data!
For example:

    
    
    >>> with hadoop_open('gs://my_bucket/results.csv', 'r') as f: 
    ...     pandas_df.to_csv(f)
    

The provided source and destination file paths must be URIs (uniform resource
identifiers).

Parameters:

    

  * **src** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Source file URI.

  * **dest** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Destination file URI.

hail.utils.hadoop_exists(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_exists)

    

Returns `True` if path exists.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.hadoop_is_file(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_is_file)

    

Returns `True` if path both exists and is a file.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.hadoop_is_dir(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_is_dir)

    

Returns `True` if path both exists and is a directory.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.hadoop_stat(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_stat)

    

Returns information about the file or directory at a given path.

Notes

Raises an error if path does not exist.

The resulting dictionary contains the following data:

  * is_dir ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Path is a directory.

  * size_bytes ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Size in bytes.

  * size ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Size as a readable string.

  * modification_time ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Time of last file modification.

  * owner ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Owner.

  * path ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)")

hail.utils.hadoop_ls(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_ls)

    

Returns information about files at path.

Notes

Raises an error if path does not exist.

If path is a file, returns a list with one element. If path is a directory,
returns an element for each file contained in path (does not search
recursively).

Each dict element of the result list contains the following data:

  * is_dir ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Path is a directory.

  * size_bytes ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Size in bytes.

  * size ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Size as a readable string.

  * modification_time ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Time of last file modification.

  * owner ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Owner.

  * path ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") [[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)")]

hail.utils.hadoop_scheme_supported(_scheme_)[[source]](../_modules/hail/utils/hadoop_utils.html#hadoop_scheme_supported)

    

Returns `True` if the Hadoop filesystem supports URLs with the given scheme.

Examples

    
    
    >>> hadoop_scheme_supported('gs') 
    

Notes

URLs with the https scheme are only supported if they are specifically Azure
Blob Storage URLs of the form
https://<ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<PATH>

Parameters:

    

**scheme** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)"))

Returns:

    

[`bool`](../functions/constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")

hail.utils.copy_log(_path_)[[source]](../_modules/hail/utils/hadoop_utils.html#copy_log)

    

Attempt to copy the session log to a hadoop-API-compatible location.

Examples

Specify a manual path:

    
    
    >>> hl.copy_log('gs://my-bucket/analysis-10-jan19.log')  
    INFO: copying log to 'gs://my-bucket/analysis-10-jan19.log'...
    

Copy to a directory:

    
    
    >>> hl.copy_log('gs://my-bucket/')  
    INFO: copying log to 'gs://my-bucket/hail-20180924-2018-devel-46e5fad57524.log'...
    

Notes

Since Hail cannot currently log directly to distributed file systems, this
function is provided as a utility for offloading logs from ephemeral nodes.

If path is a directory, then the log file will be copied using its base name
to the directory (e.g. `/home/hail.log` would be copied as `gs://my-
bucket/hail.log` if path is `gs://my-bucket`.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)"))

hail.utils.range_table(_n_ , _n_partitions
=None_)[[source]](../_modules/hail/utils/misc.html#range_table)

    

Construct a table with the row index and no other fields.

Examples

    
    
    >>> df = hl.utils.range_table(100)
    
    
    
    >>> df.count()
    100
    

Notes

The resulting table contains one field:

>   * idx ([`tint32`](../types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Row index (key).
>
>

This method is meant for testing and learning, and is not optimized for
production performance.

Parameters:

    

  * **n** (_int_) – Number of rows.

  * **n_partitions** (_int, optional_) – Number of partitions (uses Spark default parallelism if None).

Returns:

    

```Table```

hail.utils.range_matrix_table(_n_rows_ , _n_cols_ , _n_partitions
=None_)[[source]](../_modules/hail/utils/misc.html#range_matrix_table)

    

Construct a matrix table with row and column indices and no entry fields.

Examples

    
    
    >>> range_ds = hl.utils.range_matrix_table(n_rows=100, n_cols=10)
    
    
    
    >>> range_ds.count_rows()
    100
    
    
    
    >>> range_ds.count_cols()
    10
    

Notes

The resulting matrix table contains the following fields:

>   * row_idx ([`tint32`](../types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Row index (row key).
>
>   * col_idx ([`tint32`](../types.html#hail.expr.types.tint32
> "hail.expr.types.tint32")) - Column index (column key).
>
>

It contains no entry fields.

This method is meant for testing and learning, and is not optimized for
production performance.

Parameters:

    

  * **n_rows** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of rows.

  * **n_cols** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Number of columns.

  * **n_partitions** (_int, optional_) – Number of partitions (uses Spark default parallelism if None).

Returns:

    

```MatrixTable```

hail.utils.get_1kg(_output_dir_ , _overwrite
=False_)[[source]](../_modules/hail/utils/tutorial.html#get_1kg)

    

Download subset of the [1000 Genomes](http://www.internationalgenome.org/)
dataset and sample annotations.

Notes

The download is about 15M.

Parameters:

    

  * **output_dir** – Directory in which to write data.

  * **overwrite** – If `True`, overwrite any existing files/directories at output_dir.

hail.utils.get_hgdp(_output_dir_ , _overwrite
=False_)[[source]](../_modules/hail/utils/tutorial.html#get_hgdp)

    

Download subset of the [Human Genome Diversity
Panel](https://www.internationalgenome.org/data-portal/data-collection/hgdp/)
dataset and sample annotations.

Notes

The download is about 30MB.

Parameters:

    

  * **output_dir** – Directory in which to write data.

  * **overwrite** – If `True`, overwrite any existing files/directories at output_dir.

hail.utils.get_movie_lens(_output_dir_ , _overwrite
=False_)[[source]](../_modules/hail/utils/tutorial.html#get_movie_lens)

    

Download public Movie Lens dataset.

Notes

The download is about 6M.

See the [MovieLens website](https://grouplens.org/datasets/movielens/100k/)
for more information about this dataset.

Parameters:

    

  * **output_dir** – Directory in which to write data.

  * **overwrite** – If `True`, overwrite existing files/directories at those locations.

hail.utils.ANY_REGION

    

Built-in mutable sequence.

If no argument is given, the constructor creates a new empty list. The
argument must be an iterable if specified.

---

# linalg

**File formats and interface for numeric matrices are experimental**.
Improvements to Hail 0.2 may necessitate re-writing pipelines and files to
maintain compatibility.

Classes

```BlockMatrix``` | Hail's block-distributed matrix of ```tfloat64``` elements.  
---|---  
  
Modules

  * ``utils``

---

# stats

Classes

```LinearMixedModel``` | Class representing a linear mixed model.  
---|---

---

# genetics  
  
Classes

```AlleleType``` | An enumeration for allele type.  
---|---  
```Call``` | An object that represents an individual's call at a genomic locus.  
```Locus``` | An object that represents a location in the genome.  
```Pedigree``` | Class containing a list of trios, with extra functionality.  
```ReferenceGenome``` | An object that represents a [reference genome](https://en.wikipedia.org/wiki/Reference_genome).  
```Trio``` | Class containing information about nuclear family relatedness and sex.

---

# Plot  
  
Warning

Plotting functionality is in early stages and is experimental. Interfaces will
change regularly.

Plotting in Hail is easy. Hail’s plot functions utilize Bokeh plotting
libraries to create attractive, interactive figures. Plotting functions in
this module return a Bokeh Figure, so you can call a method to plot your data
and then choose to extend the plot however you like by interacting directly
with Bokeh. See the GWAS tutorial for examples.

Plot functions in Hail accept data in the form of either Python objects or
```Table``` and
```MatrixTable```
fields.

`cdf` | Create a cumulative density plot.  
---|---  
`pdf` |   
`smoothed_pdf` | Create a density plot.  
`histogram` | Create a histogram.  
`cumulative_histogram` | Create a cumulative histogram.  
`histogram2d` | Plot a two-dimensional histogram.  
`scatter` | Create an interactive scatter plot.  
`qq` | Create a Quantile-Quantile plot.  
`manhattan` | Create a Manhattan plot.  
`output_notebook` | Configure the Bokeh output state to generate output in notebook cells when [`bokeh.io.show()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.show "\(in Bokeh v3.1.0\)") is called.  
`visualize_missingness` | Visualize missingness in a MatrixTable.  
  
hail.plot.cdf(_data_ , _k =350_, _legend =None_, _title =None_, _normalize
=True_, _log =False_)[[source]](_modules/hail/plot/plots.html#cdf)

    

Create a cumulative density plot.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **k** (_int_) – Accuracy parameter (passed to ```approx_cdf()```).

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **normalize** (_bool_) – Whether or not the cumulative data should be normalized.

  * **log** (_bool_) – Whether or not the y-axis should be of type log.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.pdf(_data_ , _k =1000_, _confidence =5_, _legend =None_, _title
=None_, _log =False_, _interactive
=False_)[[source]](_modules/hail/plot/plots.html#pdf)

    

hail.plot.smoothed_pdf(_data_ , _k =350_, _smoothing =0.5_, _legend =None_,
_title =None_, _log =False_, _interactive =False_, _figure
=None_)[[source]](_modules/hail/plot/plots.html#smoothed_pdf)

    

Create a density plot.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **k** (_int_) – Accuracy parameter.

  * **smoothing** (_float_) – Degree of smoothing.

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **log** (_bool_) – Plot the log10 of the bin counts.

  * **interactive** (_bool_) – If True, return a handle to pass to [`bokeh.io.show()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.show "\(in Bokeh v3.1.0\)").

  * **figure** ([`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure "\(in Bokeh v3.1.0\)")) – If not None, add density plot to figure. Otherwise, create a new figure.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.histogram(_data_ , _range =None_, _bins =50_, _legend =None_, _title
=None_, _log =False_, _interactive
=False_)[[source]](_modules/hail/plot/plots.html#histogram)

    

Create a histogram.

Notes

data can be a
[`Float64Expression`](hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression"), or the result of the
[`hist()`](aggregators.html#hail.expr.aggregators.hist
"hail.expr.aggregators.hist") or
[`approx_cdf()`](aggregators.html#hail.expr.aggregators.approx_cdf
"hail.expr.aggregators.approx_cdf") aggregators.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **range** (_Tuple[float]_) – Range of x values in the histogram.

  * **bins** (_int_) – Number of bins in the histogram.

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **log** (_bool_) – Plot the log10 of the bin counts.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.cumulative_histogram(_data_ , _range =None_, _bins =50_, _legend
=None_, _title =None_, _normalize =True_, _log
=False_)[[source]](_modules/hail/plot/plots.html#cumulative_histogram)

    

Create a cumulative histogram.

Parameters:

    

  * **data** (```Struct``` or ```Float64Expression```) – Sequence of data to plot.

  * **range** (_Tuple[float]_) – Range of x values in the histogram.

  * **bins** (_int_) – Number of bins in the histogram.

  * **legend** (_str_) – Label of data on the x-axis.

  * **title** (_str_) – Title of the histogram.

  * **normalize** (_bool_) – Whether or not the cumulative data should be normalized.

  * **log** (_bool_) – Whether or not the y-axis should be of type log.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.histogram2d(_x_ , _y_ , _bins =40_, _range =None_, _title =None_,
_width =600_, _height =600_, _colors =('#eff3ff', '#c6dbef', '#9ecae1',
'#6baed6', '#4292c6', '#2171b5', '#084594')_, _log
=False_)[[source]](_modules/hail/plot/plots.html#histogram2d)

    

Plot a two-dimensional histogram.

`x` and `y` must both be a
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") from the same
```Table```.

If `x_range` or `y_range` are not provided, the function will do a pass
through the data to determine min and max of each variable.

Examples

    
    
    >>> ht = hail.utils.range_table(1000).annotate(x=hail.rand_norm(), y=hail.rand_norm())
    >>> p_hist = hail.plot.histogram2d(ht.x, ht.y)
    
    
    
    >>> ht = hail.utils.range_table(1000).annotate(x=hail.rand_norm(), y=hail.rand_norm())
    >>> p_hist = hail.plot.histogram2d(ht.x, ht.y, bins=10, range=((0, 1), None))
    

Parameters:

    

  * **x** (```NumericExpression```) – Expression for x-axis (from a Hail table).

  * **y** (```NumericExpression```) – Expression for y-axis (from the same Hail table as `x`).

  * **bins** (_int or [int, int]_) – The bin specification: \- If int, the number of bins for the two dimensions (nx = ny = bins). \- If [int, int], the number of bins in each dimension (nx, ny = bins). The default value is 40.

  * **range** (_None or ((float, float), (float, float))_) – The leftmost and rightmost edges of the bins along each dimension: ((xmin, xmax), (ymin, ymax)). All values outside of this range will be considered outliers and not tallied in the histogram. If this value is None, or either of the inner lists is None, the range will be computed from the data.

  * **width** (_int_) – Plot width (default 600px).

  * **height** (_int_) – Plot height (default 600px).

  * **title** (_str_) – Title of the plot.

  * **colors** (_Sequence[str]_) – List of colors (hex codes, or strings as described [here](https://bokeh.pydata.org/en/latest/docs/reference/colors.html)). Compatible with one of the many built-in palettes available [here](https://bokeh.pydata.org/en/latest/docs/reference/palettes.html).

  * **log** (_bool_) – Plot the log10 of the bin counts.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

hail.plot.scatter(_x_ , _y_ , _label =None_, _title =None_, _xlabel =None_,
_ylabel =None_, _size =4_, _legend =True_, _hover_fields =None_, _colors
=None_, _width =800_, _height =800_, _collect_all =None_, _n_divisions =500_,
_missing_label ='NA'_)[[source]](_modules/hail/plot/plots.html#scatter)

    

Create an interactive scatter plot.

`x` and `y` must both be either: \- a
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") from the same
```Table```. \- a tuple (str,
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) from the same
```Table```. If passed as a tuple the
first element is used as the hover label.

If no label or a single label is provided, then returns
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") Otherwise returns a
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)") containing: \- a
[`bokeh.models.widgets.inputs.Select`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/widgets/inputs.html#bokeh.models.Select
"\(in Bokeh v3.1.0\)") dropdown selection widget for labels \- a
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") containing the interactive scatter plot

Points will be colored by one of the labels defined in the `label` using the
color scheme defined in the corresponding entry of `colors` if provided
(otherwise a default scheme is used). To specify your color mapper, check [the
bokeh
documentation](https://bokeh.pydata.org/en/latest/docs/reference/colors.html)
for CategoricalMapper for categorical labels, and for LinearColorMapper and
LogColorMapper for continuous labels. For categorical labels, clicking on one
of the items in the legend will hide/show all points with the corresponding
label. Note that using many different labelling schemes in the same plots,
particularly if those labels contain many different classes could slow down
the plot interactions.

Hovering on points will display their coordinates, labels and any additional
fields specified in `hover_fields`.

Parameters:

    

  * **x** (```NumericExpression``` or (str, ```NumericExpression```)) – List of x-values to be plotted.

  * **y** (```NumericExpression``` or (str, ```NumericExpression```)) – List of y-values to be plotted.

  * **label** (```Expression``` or Dict``str, [`Expression```]], optional) – Either a single expression (if a single label is desired), or a dictionary of label name -> label value for x and y values. Used to color each point w.r.t its label. When multiple labels are given, a dropdown will be displayed with the different options. Can be used with categorical or continuous expressions.

  * **title** (_str, optional_) – Title of the scatterplot.

  * **xlabel** (_str, optional_) – X-axis label.

  * **ylabel** (_str, optional_) – Y-axis label.

  * **size** (_int_) – Size of markers in screen space units.

  * **legend** (_bool_) – Whether or not to show the legend in the resulting figure.

  * **hover_fields** (Dict``str, [`Expression```], optional) – Extra fields to be displayed when hovering over a point on the plot.

  * **colors** ([`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)") or Dict[str, [`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)")], optional) – If a single label is used, then this can be a color mapper, if multiple labels are used, then this should be a Dict of label name -> color mapper. Used to set colors for the labels defined using `label`. If not used at all, or label names not appearing in this dict will be colored using a default color scheme.

  * **width** (_int_) – Plot width

  * **height** (_int_) – Plot height

  * **collect_all** (_bool, optional_) – Deprecated. Use n_divisions instead.

  * **n_divisions** (_int, optional_) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints. Use None to collect all points.

  * **missing_label** (_str_) – Label to use when a point is missing data for a categorical label

Returns:

    

[`bokeh.models.Plot`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/plots.html#bokeh.models.Plot
"\(in Bokeh v3.1.0\)") if no label or a single label was given, otherwise
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)")

hail.plot.qq(_pvals_ , _label =None_, _title ='Q-Q plot'_, _xlabel ='Expected
-log10(p)'_, _ylabel ='Observed -log10(p)'_, _size =6_, _legend =True_,
_hover_fields =None_, _colors =None_, _width =800_, _height =800_,
_collect_all =None_, _n_divisions =500_, _missing_label
='NA'_)[[source]](_modules/hail/plot/plots.html#qq)

    

Create a Quantile-Quantile plot. (<https://en.wikipedia.org/wiki/Q-Q_plot>)

If no label or a single label is provided, then returns
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") Otherwise returns a
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)") containing: \- a
[`bokeh.models.widgets.inputs.Select`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/widgets/inputs.html#bokeh.models.Select
"\(in Bokeh v3.1.0\)") dropdown selection widget for labels \- a
[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") containing the interactive qq plot

Points will be colored by one of the labels defined in the `label` using the
color scheme defined in the corresponding entry of `colors` if provided
(otherwise a default scheme is used). To specify your color mapper, check [the
bokeh
documentation](https://bokeh.pydata.org/en/latest/docs/reference/colors.html)
for CategoricalMapper for categorical labels, and for LinearColorMapper and
LogColorMapper for continuous labels. For categorical labels, clicking on one
of the items in the legend will hide/show all points with the corresponding
label. Note that using many different labelling schemes in the same plots,
particularly if those labels contain many different classes could slow down
the plot interactions.

Hovering on points will display their coordinates, labels and any additional
fields specified in `hover_fields`.

Parameters:

    

  * **pvals** (```NumericExpression```) – List of x-values to be plotted.

  * **label** (```Expression``` or Dict``str, [`Expression```]]) – Either a single expression (if a single label is desired), or a dictionary of label name -> label value for x and y values. Used to color each point w.r.t its label. When multiple labels are given, a dropdown will be displayed with the different options. Can be used with categorical or continuous expressions.

  * **title** (_str, optional_) – Title of the scatterplot.

  * **xlabel** (_str, optional_) – X-axis label.

  * **ylabel** (_str, optional_) – Y-axis label.

  * **size** (_int_) – Size of markers in screen space units.

  * **legend** (_bool_) – Whether or not to show the legend in the resulting figure.

  * **hover_fields** (Dict``str, [`Expression```], optional) – Extra fields to be displayed when hovering over a point on the plot.

  * **colors** ([`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)") or Dict[str, [`bokeh.models.mappers.ColorMapper`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/mappers.html#bokeh.models.ColorMapper "\(in Bokeh v3.1.0\)")], optional) – If a single label is used, then this can be a color mapper, if multiple labels are used, then this should be a Dict of label name -> color mapper. Used to set colors for the labels defined using `label`. If not used at all, or label names not appearing in this dict will be colored using a default color scheme.

  * **width** (_int_) – Plot width

  * **height** (_int_) – Plot height

  * **collect_all** (_bool_) – Deprecated. Use n_divisions instead.

  * **n_divisions** (_int, optional_) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints. Use None to collect all points.

  * **missing_label** (_str_) – Label to use when a point is missing data for a categorical label

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)") if no label or a single label was given, otherwise
[`bokeh.models.layouts.Column`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/layouts.html#bokeh.models.Column
"\(in Bokeh v3.1.0\)")

hail.plot.manhattan(_pvals_ , _locus =None_, _title =None_, _size =4_,
_hover_fields =None_, _collect_all =None_, _n_divisions =500_,
_significance_line
=5e-08_)[[source]](_modules/hail/plot/plots.html#manhattan)

    

Create a Manhattan plot. (<https://en.wikipedia.org/wiki/Manhattan_plot>)

Parameters:

    

  * **pvals** (```Float64Expression```) – P-values to be plotted.

  * **locus** (```LocusExpression```, optional) – Locus values to be plotted.

  * **title** (_str, optional_) – Title of the plot.

  * **size** (_int_) – Size of markers in screen space units.

  * **hover_fields** (Dict``str, [`Expression```], optional) – Dictionary of field names and values to be shown in the HoverTool of the plot.

  * **collect_all** (_bool, optional_) – Deprecated - use n_divisions instead.

  * **n_divisions** (_int, optional._) – Factor by which to downsample (default value = 500). A lower input results in fewer output datapoints. Use None to collect all points.

  * **significance_line** (_float, optional_) – p-value at which to add a horizontal, dotted red line indicating genome-wide significance. If `None`, no line is added.

Returns:

    

[`bokeh.models.Plot`](https://docs.bokeh.org/en/3.1.0/docs/reference/models/plots.html#bokeh.models.Plot
"\(in Bokeh v3.1.0\)")

hail.plot.output_notebook()[[source]](_modules/hail/plot/plots.html#output_notebook)

    

Configure the Bokeh output state to generate output in notebook cells when
[`bokeh.io.show()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.show
"\(in Bokeh v3.1.0\)") is called. Calls
[`bokeh.io.output_notebook()`](https://docs.bokeh.org/en/3.1.0/docs/reference/io.html#bokeh.io.output_notebook
"\(in Bokeh v3.1.0\)").

hail.plot.visualize_missingness(_entry_field_ , _row_field =None_,
_column_field =None_, _window =6000000_, _plot_width =1800_, _plot_height
=900_)[[source]](_modules/hail/plot/plots.html#visualize_missingness)

    

Visualize missingness in a MatrixTable.

Inspired by
[naniar](https://cran.r-project.org/web/packages/naniar/index.html).

Row field is windowed by default, and missingness is aggregated over this
window to generate a proportion defined. This windowing is set to 6,000,000 by
default, so that the human genome is divided into ~500 rows. With ~2,000
columns, this function returns a sensibly-sized plot with this windowing.

Warning

Generating a plot with more than ~1M points takes a long time for Bokeh to
render. Consider windowing carefully.

Parameters:

    

  * **entry_field** (```Expression```) – Field for which to check missingness.

  * **row_field** (```NumericExpression``` or ```LocusExpression```) – Row field to use for y-axis (can be windowed). If not provided, the row key will be used.

  * **column_field** (```StringExpression```) – Column field to use for x-axis. If not provided, the column key will be used.

  * **window** (_int, optional_) – Size of window to summarize by `row_field`. If set to None, each field will be used individually.

  * **plot_width** (_int_) – Plot width in px.

  * **plot_height** (_int_) – Plot height in px.

Returns:

    

[`bokeh.plotting.figure`](https://docs.bokeh.org/en/3.1.0/docs/reference/plotting/figure.html#bokeh.plotting.figure
"\(in Bokeh v3.1.0\)")

---

# Plotting With hail.ggplot Overview

Warning

Plotting functionality is in early stages and is experimental.

The `hl.ggplot` module is designed based on R’s tidyverse `ggplot2` library.
This module provides a subset of `ggplot2`’s functionality to allow users to
generate plots in much the same way they would in `ggplot2`.

This module is intended to be a new, more flexible way of plotting compared to
the `hl.plot` module. This module currently uses plotly to generate plots, as
opposed to `hl.plot`, which uses bokeh.

Core functions

`ggplot` | Create the initial plot object.  
---|---  
`aes` | Create an aesthetic mapping  
`coord_cartesian` | Set the boundaries of the plot.  
  
hail.ggplot.ggplot(_table_ , _mapping
={}_)[[source]](../_modules/hail/ggplot/ggplot.html#ggplot)

    

Create the initial plot object.

This function is the beginning of all plots using the `hail.ggplot` interface.
Plots are constructed by calling this function, then adding attributes to the
plot to get the desired result.

Examples

Create a y = x^2 scatter plot

    
    
    >>> ht = hl.utils.range_table(10)
    >>> ht = ht.annotate(squared = ht.idx**2)
    >>> my_plot = hl.ggplot.ggplot(ht, hl.ggplot.aes(x=ht.idx, y=ht.squared)) + hl.ggplot.geom_point()
    

Parameters:

    

  * **table** – The table containing the data to plot.

  * **mapping** – Default list of aesthetic mappings from table data to plot attributes.

Returns:

    

`GGPlot`

hail.ggplot.aes(_** kwargs_)[[source]](../_modules/hail/ggplot/aes.html#aes)

    

Create an aesthetic mapping

Parameters:

    

**kwargs** – Map aesthetic names to hail expressions based on table’s plot.

Returns:

    

`Aesthetic` – The aesthetic mapping to be applied.

hail.ggplot.coord_cartesian(_xlim =None_, _ylim
=None_)[[source]](../_modules/hail/ggplot/coord_cartesian.html#coord_cartesian)

    

Set the boundaries of the plot.

Parameters:

    

  * **xlim** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") with two int) – The minimum and maximum x value to show on the plot.

  * **ylim** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") with two int) – The minimum and maximum y value to show on the plot.

Returns:

    

`FigureAttribute` – The coordinate attribute to be applied.

Geoms

`geom_point` | Create a scatter plot.  
---|---  
`geom_line` | Create a line plot.  
`geom_text` | Create a scatter plot where each point is text from the `text` aesthetic.  
`geom_bar` | Create a bar chart that counts occurrences of the various values of the `x` aesthetic.  
`geom_col` | Create a bar chart that uses bar heights specified in y aesthetic.  
`geom_histogram` | Creates a histogram.  
`geom_density` | Creates a smoothed density plot.  
`geom_hline` | Plots a horizontal line at `yintercept`.  
`geom_vline` | Plots a vertical line at `xintercept`.  
`geom_area` | Creates a line plot with the area between the line and the x-axis filled in.  
`geom_ribbon` | Creates filled in area between two lines specified by x, ymin, and ymax  
  
hail.ggplot.geom_point(_mapping ={}_, _*_ , _color =None_, _size =None_,
_alpha =None_, _shape
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_point)

    

Create a scatter plot.

Supported aesthetics: `x`, `y`, `color`, `alpha`, `tooltip`, `shape`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_line(_mapping ={}_, _*_ , _color =None_, _size =None_, _alpha
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_line)

    

Create a line plot.

Supported aesthetics: `x`, `y`, `color`, `tooltip`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_text(_mapping ={}_, _*_ , _color =None_, _size =None_, _alpha
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_text)

    

Create a scatter plot where each point is text from the `text` aesthetic.

Supported aesthetics: `x`, `y`, `label`, `color`, `tooltip`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_bar(_mapping ={}_, _*_ , _fill =None_, _color =None_, _alpha
=None_, _position ='stack'_, _size
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_bar)

    

Create a bar chart that counts occurrences of the various values of the `x`
aesthetic.

Supported aesthetics: `x`, `color`, `fill`, `weight`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_col(_mapping ={}_, _*_ , _fill =None_, _color =None_, _alpha
=None_, _position ='stack'_, _size
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_col)

    

Create a bar chart that uses bar heights specified in y aesthetic.

Supported aesthetics: `x`, `y`, `color`, `fill`

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_histogram(_mapping ={}_, _*_ , _min_val =None_, _max_val
=None_, _bins =None_, _fill =None_, _color =None_, _alpha =None_, _position
='stack'_, _size
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_histogram)

    

Creates a histogram.

Note: this function currently does not support same interface as R’s ggplot.

Supported aesthetics: `x`, `color`, `fill`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **min_val** (int or float) – Minimum value to include in histogram

  * **max_val** (int or float) – Maximum value to include in histogram

  * **bins** (int) – Number of bins to plot. 30 by default.

  * **fill** – A single fill color for all bars of histogram, overrides `fill` aesthetic.

  * **color** – A single outline color for all bars of histogram, overrides `color` aesthetic.

  * **alpha** (float) – A measure of transparency between 0 and 1.

  * **position** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Tells how to deal with different groups of data at same point. Options are “stack” and “dodge”.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_density(_mapping ={}_, _*_ , _k =1000_, _smoothing =0.5_,
_fill =None_, _color =None_, _alpha =None_, _smoothed
=False_)[[source]](../_modules/hail/ggplot/geoms.html#geom_density)

    

Creates a smoothed density plot.

This method uses the hl.agg.approx_cdf aggregator to compute a sketch of the
distribution of the values of x. It then uses an ad hoc method to estimate a
smoothed pdf consistent with that cdf.

Note: this function currently does not support same interface as R’s ggplot.

Supported aesthetics: `x`, `color`, `fill`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **k** (int) – Passed to the approx_cdf aggregator. The size of the aggregator scales linearly with k. The default value of 1000 is likely sufficient for most uses.

  * **smoothing** (float) – Controls the amount of smoothing applied.

  * **fill** – A single fill color for all density plots, overrides `fill` aesthetic.

  * **color** – A single line color for all density plots, overrides `color` aesthetic.

  * **alpha** (float) – A measure of transparency between 0 and 1.

  * **smoothed** (boolean) – If true, attempts to fit a smooth kernel density estimator. If false, uses a custom method do generate a variable width histogram directly from the approx_cdf results.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_hline(_yintercept_ , _*_ , _linetype ='solid'_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_hline)

    

Plots a horizontal line at `yintercept`.

Parameters:

    

  * **yintercept** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Location to draw line.

  * **linetype** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Type of line to draw. Choose from “solid”, “dashed”, “dotted”, “longdash”, “dotdash”.

  * **color** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Color of line to draw, black by default.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_vline(_xintercept_ , _*_ , _linetype ='solid'_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_vline)

    

Plots a vertical line at `xintercept`.

Parameters:

    

  * **xintercept** ([`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – Location to draw line.

  * **linetype** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Type of line to draw. Choose from “solid”, “dashed”, “dotted”, “longdash”, “dotdash”.

  * **color** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Color of line to draw, black by default.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_area(_mapping ={}_, _fill =None_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_area)

    

Creates a line plot with the area between the line and the x-axis filled in.

Supported aesthetics: `x`, `y`, `fill`, `color`, `tooltip`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **fill** – Color of fill to draw, black by default. Overrides `fill` aesthetic.

  * **color** – Color of line to draw outlining non x-axis facing side, none by default. Overrides `color` aesthetic.

Returns:

    

`FigureAttribute` – The geom to be applied.

hail.ggplot.geom_ribbon(_mapping ={}_, _fill =None_, _color
=None_)[[source]](../_modules/hail/ggplot/geoms.html#geom_ribbon)

    

Creates filled in area between two lines specified by x, ymin, and ymax

Supported aesthetics: `x`, `ymin`, `ymax`, `color`, `fill`, `tooltip`

Parameters:

    

  * **mapping** (`Aesthetic`) – Any aesthetics specific to this geom.

  * **fill** – Color of fill to draw, black by default. Overrides `fill` aesthetic.

  * **color** – Color of line to draw outlining both side, none by default. Overrides `color` aesthetic.

  * _return:_

  * **``FigureAttribute``** – The geom to be applied.

Scales

`scale_x_continuous` | The default continuous x scale.  
---|---  
`scale_x_discrete` | The default discrete x scale.  
`scale_x_genomic` | The default genomic x scale.  
`scale_x_log10` | Transforms x axis to be log base 10 scaled.  
`scale_x_reverse` | Transforms x-axis to be vertically reversed.  
`scale_y_continuous` | The default continuous y scale.  
`scale_y_discrete` | The default discrete y scale.  
`scale_y_log10` | Transforms y-axis to be log base 10 scaled.  
`scale_y_reverse` | Transforms y-axis to be vertically reversed.  
`scale_color_continuous` | The default continuous color scale.  
`scale_color_discrete` | The default discrete color scale.  
`scale_color_hue` | Map discrete colors to evenly placed positions around the color wheel.  
`scale_color_manual` | A color scale that assigns strings to colors using the pool of colors specified as values.  
`scale_color_identity` | A color scale that assumes the expression specified in the `color` aesthetic can be used as a color.  
`scale_fill_continuous` | The default continuous fill scale.  
`scale_fill_discrete` | The default discrete fill scale.  
`scale_fill_hue` | Map discrete fill colors to evenly placed positions around the color wheel.  
`scale_fill_manual` | A color scale that assigns strings to fill colors using the pool of colors specified as values.  
`scale_fill_identity` | A color scale that assumes the expression specified in the `fill` aesthetic can be used as a fill color.  
  
hail.ggplot.scale_x_continuous(_name =None_, _breaks =None_, _labels =None_,
_trans
='identity'_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_continuous)

    

The default continuous x scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on x-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – The locations to draw ticks on the x-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

  * **trans** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The transformation to apply to the x-axis. Supports “identity”, “reverse”, “log10”.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_discrete(_name =None_, _breaks =None_, _labels
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_discrete)

    

The default discrete x scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on x-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The locations to draw ticks on the x-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_genomic(_reference_genome_ , _name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_genomic)

    

The default genomic x scale. This is used when the `x` aesthetic corresponds
to a
[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression").

Parameters:

    

  * **reference_genome** – The reference genome being used.

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on y-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_log10(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_log10)

    

Transforms x axis to be log base 10 scaled.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on x-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_x_reverse(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_x_reverse)

    

Transforms x-axis to be vertically reversed.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on x-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_continuous(_name =None_, _breaks =None_, _labels =None_,
_trans
='identity'_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_continuous)

    

The default continuous y scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on y-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`float`](https://docs.python.org/3.9/library/functions.html#float "\(in Python v3.9\)")) – The locations to draw ticks on the y-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

  * **trans** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The transformation to apply to the y-axis. Supports “identity”, “reverse”, “log10”.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_discrete(_name =None_, _breaks =None_, _labels
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_discrete)

    

The default discrete y scale.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The label to show on y-axis

  * **breaks** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The locations to draw ticks on the y-axis.

  * **labels** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The labels of the ticks on the axis.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_log10(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_log10)

    

Transforms y-axis to be log base 10 scaled.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on y-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_y_reverse(_name
=None_)[[source]](../_modules/hail/ggplot/scale.html#scale_y_reverse)

    

Transforms y-axis to be vertically reversed.

Parameters:

    

**name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The label to show on y-axis

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_continuous()[[source]](../_modules/hail/ggplot/scale.html#scale_color_continuous)

    

The default continuous color scale. This linearly interpolates colors between
the min and max observed values.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_discrete()[[source]](../_modules/hail/ggplot/scale.html#scale_color_discrete)

    

The default discrete color scale. This maps each discrete value to a color.
Equivalent to scale_color_hue.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_hue()[[source]](../_modules/hail/ggplot/scale.html#scale_color_hue)

    

Map discrete colors to evenly placed positions around the color wheel.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_manual(_*_ ,
_values_)[[source]](../_modules/hail/ggplot/scale.html#scale_color_manual)

    

A color scale that assigns strings to colors using the pool of colors
specified as values.

Parameters:

    

**values** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – The colors to choose when assigning values to colors.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_color_identity()[[source]](../_modules/hail/ggplot/scale.html#scale_color_identity)

    

A color scale that assumes the expression specified in the `color` aesthetic
can be used as a color.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_continuous()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_continuous)

    

The default continuous fill scale. This linearly interpolates colors between
the min and max observed values.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_discrete()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_discrete)

    

The default discrete fill scale. This maps each discrete value to a fill
color.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_hue()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_hue)

    

Map discrete fill colors to evenly placed positions around the color wheel.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_manual(_*_ ,
_values_)[[source]](../_modules/hail/ggplot/scale.html#scale_fill_manual)

    

A color scale that assigns strings to fill colors using the pool of colors
specified as values.

Parameters:

    

**values** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – The colors to choose when assigning values to colors.

Returns:

    

`FigureAttribute` – The scale to be applied.

hail.ggplot.scale_fill_identity()[[source]](../_modules/hail/ggplot/scale.html#scale_fill_identity)

    

A color scale that assumes the expression specified in the `fill` aesthetic
can be used as a fill color.

Returns:

    

`FigureAttribute` – The scale to be applied.

Facets

`facet_wrap` | Introduce a one dimensional faceting on specified fields.  
---|---  
`vars` | 

Parameters:

    ***args** (```hail.expr.Expression```) -- Fields to facet by.  
  
hail.ggplot.facet_wrap(_facets_ , _*_ , _nrow =None_, _ncol =None_, _scales
='fixed'_)[[source]](../_modules/hail/ggplot/facets.html#facet_wrap)

    

Introduce a one dimensional faceting on specified fields.

Parameters:

    

  * **facets** (```hail.expr.StructExpression``` created by hl.ggplot.vars function.) – The fields to facet on.

  * **nrow** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – The number of rows into which the facets will be spread. Will be ignored if ncol is set.

  * **ncol** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – The number of columns into which the facets will be spread.

  * **scales** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Whether the scales are the same across facets. For more information and a list of supported options, see [the ggplot documentation](https://ggplot2-book.org/facet.html#controlling-scales).

Returns:

    

`FigureAttribute` – The faceter.

hail.ggplot.vars(_*
args_)[[source]](../_modules/hail/ggplot/facets.html#vars)

    

Parameters:

    

***args**
([`hail.expr.Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Fields to facet by.

Returns:

    

[`hail.expr.StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A struct to pass to a faceter.

Labels

`xlab`(label) | Sets the x-axis label of a plot.  
---|---  
`ylab`(label) | Sets the y-axis label of a plot.  
`ggtitle`(label) | Sets the title of a plot.  
  
hail.ggplot.xlab(_label_)[[source]](../_modules/hail/ggplot/labels.html#xlab)

    

Sets the x-axis label of a plot.

Parameters:

    

**label** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The desired x-axis label of the plot.

Returns:

    

`FigureAttribute` – Label object to change the x-axis label.

hail.ggplot.ylab(_label_)[[source]](../_modules/hail/ggplot/labels.html#ylab)

    

Sets the y-axis label of a plot.

Parameters:

    

**label** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The desired y-axis label of the plot.

Returns:

    

`FigureAttribute` – Label object to change the y-axis label.

hail.ggplot.ggtitle(_label_)[[source]](../_modules/hail/ggplot/labels.html#ggtitle)

    

Sets the title of a plot.

Parameters:

    

**label** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The desired title of the plot.

Returns:

    

`FigureAttribute` – Label object to change the title.

Classes

_class _hail.ggplot.GGPlot(_ht_ , _aes_ , _geoms=[]_ , _labels=
<hail.ggplot.labels.Labels object>_, _coord_cartesian=None_ , _scales=None_ ,
_facet=None_)[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot)

    

The class representing a figure created using the `hail.ggplot` module.

Create one by using `ggplot()`.

to_plotly()[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot.to_plotly)

    

Turn the hail plot into a Plotly plot.

Returns:

    

_A Plotly figure that can be updated with plotly methods._

show()[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot.show)

    

Render and show the plot, either in a browser or notebook.

write_image(_path_)[[source]](../_modules/hail/ggplot/ggplot.html#GGPlot.write_image)

    

Write out this plot as an image.

This requires you to have installed the python package kaleido from pypi.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – The path to write the file to.

_class
_hail.ggplot.Aesthetic(_properties_)[[source]](../_modules/hail/ggplot/aes.html#Aesthetic)

    

_class
_hail.ggplot.FigureAttribute[[source]](../_modules/hail/ggplot/geoms.html#FigureAttribute)

---

# Variant Dataset

The [`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") is an extra layer of abstraction of the Hail Matrix
Table for working with large sequencing datasets. It was initially developed
in response to the gnomAD project’s need to combine, represent, and analyze
150,000 whole genomes. It has since been used on datasets as large as 955,000
whole exomes. The
[`VariantDatasetCombiner`](hail.vds.combiner.VariantDatasetCombiner.html#hail.vds.combiner.VariantDatasetCombiner
"hail.vds.combiner.VariantDatasetCombiner") produces a
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") by combining any number of GVCF and/or
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") files.

Warning

Hail 0.1 also had a Variant Dataset class. Although pieces of the interfaces
are similar, they should not be considered interchangeable and do not
represent the same data.

Variant Dataset

```VariantDataset``` | Class for representing cohort-level genomic data.  
---|---  
```read_vds```(path, *[, intervals, n_partitions, ...]) | Read in a ```VariantDataset``` written with ```VariantDataset.write()```.  
---|---  
```filter_samples```(vds, samples, *[, keep, ...]) | Filter samples in a ```VariantDataset```.  
```filter_variants```(vds, variants_table, *[, keep]) | Filter variants in a ```VariantDataset```, without removing reference data.  
```filter_intervals```(vds, intervals, *[, ...]) | Filter intervals in a ```VariantDataset```.  
```filter_chromosomes```(vds, *[, keep, remove, ...]) | Filter chromosomes of a ```VariantDataset``` in several possible modes.  
```sample_qc```(vds, *[, gq_bins, dp_bins, dp_field]) | Compute sample quality metrics about a ```VariantDataset```.  
```split_multi```(vds, *[, filter_changed_loci]) | Split the multiallelic variants in a ```VariantDataset```.  
```interval_coverage```(vds, intervals[, ...]) | Compute statistics about base coverage by interval.  
```impute_sex_chromosome_ploidy```(vds, ...[, ...]) | Impute sex chromosome ploidy from depth of reference or variant data within calling intervals.  
```impute_sex_chr_ploidy_from_interval_coverage```(mt, ...) | Impute sex chromosome ploidy from a precomputed interval coverage MatrixTable.  
```to_dense_mt```(vds) | Creates a single, dense ```MatrixTable``` from the split ```VariantDataset``` representation.  
```to_merged_sparse_mt```(vds, *[, ...]) | Creates a single, merged sparse ```MatrixTable``` from the split ```VariantDataset``` representation.  
```truncate_reference_blocks```(ds, *[, ...]) | Cap reference blocks at a maximum length in order to permit faster interval filtering.  
```merge_reference_blocks```(ds, equivalence_function) | Merge adjacent reference blocks according to user equivalence criteria.  
```lgt_to_gt```(lgt, la) | Transform LGT into GT using local alleles array.  
```local_to_global```(array, local_alleles, ...) | Reindex a locally-indexed array to globally-indexed.  
```store_ref_block_max_length```(vds_path) | Patches an existing VDS file to store the max reference block length for faster interval filters.  
  
Variant Dataset Combiner

```VDSMetadata``` | The path to a Variant Dataset and the number of samples within.  
---|---  
```VariantDatasetCombiner``` | A restartable and failure-tolerant method for combining one or more GVCFs and Variant Datasets.  
```new_combiner```(*, output_path, temp_path[, ...]) | Create a new ```VariantDatasetCombiner``` or load one from save_path.  
---|---  
```load_combiner```(path) | Load a ```VariantDatasetCombiner``` from path.  
  
## The data model of
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset")

A VariantDataset is the Hail implementation of a data structure called the
“scalable variant call representation”, or SVCR.

### The Scalable Variant Call Representation (SVCR)

Like the project VCF (multi-sample VCF) representation, the scalable variant
call representation is a variant-by-sample matrix of records. There are two
fundamental differences, however:

  1. The scalable variant call representation is **sparse**. It is not a dense matrix with every entry populated. Reference calls are defined as intervals (reference blocks) exactly as they appear in the original GVCFs. Compared to a VCF representation, this stores **less data but more information** , and makes it possible to keep reference information about every site in the genome, not just sites at which there is variation in the current cohort. A VariantDataset has a component table of reference information, `vds.reference_data`, which contains the sparse matrix of reference blocks. This matrix is keyed by locus (not locus and alleles), and contains an `END` field which denotes the last position included in the current reference block.

  2. The scalable variant call representation uses **local alleles**. In a VCF, the fields GT, AD, PL, etc contain information that refers to alleles in the VCF by index. At highly multiallelic sites, the number of elements in the AD/PL lists explodes to huge numbers, **even though the information content does not change**. To avoid this superlinear scaling, the SVCR renames these fields to their “local” versions: LGT, LAD, LPL, etc, and adds a new field, LA (local alleles). The information in the local fields refers to the alleles defined per row of the matrix indirectly through the LA list.

For instance, if a sample has the following information in its GVCF:

         
         Ref=G Alt=T GT=0/1 AD=5,6 PL=102,0,150
         

If the alternate alleles A,C,T are discovered in the cohort, this sample’s
entry would look like:

         
         LA=0,2 LGT=0/1 LAD=5,6 LPL=102,0,150
         

The “1” allele referred to in LGT, and the allele to which the reads in the
second position of LAD belong to, is not the allele with absolute index 1
(**C**), but rather the allele whose index is in position 1 of the LA list.
The _index_ at position 2 of the LA list is 2, and the allele with absolute
index 2 is **T**. Local alleles make it possible to keep the data small to
match its inherent information content.

### Component tables

The [`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset") is made up of two component matrix tables – the
`reference_data` and the `variant_data`.

The `reference_data` matrix table is a sparse matrix of reference blocks. The
`reference_data` matrix table has row key `locus`, but does not have an
`alleles` key or field. The column key is the sample ID. The entries indicate
regions of reference calls with similar sequencing metadata (depth, quality,
etc), starting from `vds.reference_data.locus.position` and ending at
`vds.reference_data.END` (inclusive!). There is no `GT` call field because all
calls in the reference data are implicitly homozygous reference (in the
future, a table of ploidy by interval may be included to allow for proper
representation of structural variation, but there is no standard
representation for this at current). A record from a component GVCF is
included in the `reference_data` if it defines the END INFO field (if the GT
is not reference, an error will be thrown by the Hail VDS combiner).

The `variant_data` matrix table is a sparse matrix of non-reference calls.
This table contains the complete schema from the component GVCFs, aside from
fields which are known to be defined only for reference blocks (e.g. END or
MIN_DP). A record from a component GVCF is included in the `variant_data` if
it does not define the END INFO field. This means that some records of the
`variant_data` can be no-call (`./.`) or reference, depending on the semantics
of the variant caller that produced the GVCFs.

### Building analyses on the
[`VariantDataset`](hail.vds.VariantDataset.html#hail.vds.VariantDataset
"hail.vds.VariantDataset")

Analyses operating on sequencing data can be largely grouped into three
categories by functionality used.

  1. **Analyses that use prebuilt methods**. Some analyses can be supported by using only the utility functions defined in the `hl.vds` module, like ```vds.sample_qc()```.

  2. **Analyses that use variant data and/or reference data separately.** Some pipelines need to interrogate properties of the component tables individually. Examples might include singleton analysis or burden tests (which needs only to look at the variant data) or coverage analysis (which looks only at reference data). These pipelines should explicitly extract and manipulate the component tables with `vds.variant_data` and `vds.reference_data`.

  3. **Analyses that use the full variant-by-sample matrix with variant and reference data**. Many pipelines require variant and reference data together. There are helper functions provided for producing either the sparse (containing reference blocks) or dense (reference information is filled in at each variant site) representations. For more information, see the documentation for ```vds.to_dense_mt()``` and ```vds.to_merged_sparse_mt()```.

---

# Experimental

This module serves two functions: as a staging area for extensions of Hail not
ready for inclusion in the main package, and as a library of lightly reviewed
community submissions.

At present, the experimental module is organized into a few freestanding
modules, linked immediately below, and many freestanding functions, documented
on this page.

Warning

The functionality in this module may change or disappear entirely between
different versions of Hail. If you critically depend on functionality in this
module, please create an issue to request promotion of that functionality to
non-experimental. Otherwise, that functionality may disappear!

  * ``ldscsim``

## Contribution Guidelines

Submissions from the community are welcome! The criteria for inclusion in the
experimental module are loose and subject to change:

  1. Function docstrings are required. Hail uses [NumPy style docstrings](https://www.sphinx-doc.org/en/stable/usage/extensions/example_numpy.html).

  2. Tests are not required, but are encouraged. If you do include tests, they must run in no more than a few seconds. Place tests as a class method on `Tests` in `python/tests/experimental/test_experimental.py`

  3. Code style is not strictly enforced, aside from egregious violations. We do recommend using [autopep8](https://pypi.org/project/autopep8/) though!

## Annotation Database

Classes

```hail.experimental.DB``` | An annotation database instance.  
---|---  
  
## Genetics Methods

`load_dataset`(name, version, reference_genome) | Load a genetic dataset from Hail's repository.  
---|---  
`ld_score`(entry_expr, locus_expr, radius[, ...]) | Calculate LD scores.  
`ld_score_regression`(weight_expr, ...[, ...]) | Estimate SNP-heritability and level of confounding biases from genome-wide association study (GWAS) summary statistics.  
`write_expression`(expr, path[, overwrite]) | Write an Expression.  
`read_expression`(path[, _assert_type]) | Read an ```Expression``` written with `experimental.write_expression()`.  
`filtering_allele_frequency`(ac, an, ci) | Computes a filtering allele frequency (described below) for ac and an with confidence ci.  
`hail_metadata`(t_path) | Create a metadata plot for a Hail Table or MatrixTable.  
`plot_roc_curve`(ht, scores[, tp_label, ...]) | Create ROC curve from Hail Table.  
`phase_by_transmission`(locus, alleles, ...) | Phases genotype calls in a trio based allele transmission.  
`phase_trio_matrix_by_transmission`(tm[, ...]) | Adds a phased genoype entry to a trio MatrixTable based allele transmission in the trio.  
`explode_trio_matrix`(tm[, col_keys, ...]) | Splits a trio MatrixTable back into a sample MatrixTable.  
`import_gtf`(path[, reference_genome, ...]) | Import a GTF file.  
`get_gene_intervals`([gene_symbols, gene_ids, ...]) | Get intervals of genes or transcripts.  
`export_entries_by_col`(mt, path[, ...]) | Export entries of the mt by column as separate text files.  
`pc_project`(call_expr, loadings_expr, af_expr) | Projects genotypes onto pre-computed PCs.  
  
## dplyr-inspired Methods

`gather`(ht, key, value, *fields) | Collapse fields into key-value pairs.  
---|---  
`separate`(ht, field, into, delim) | Separate a field into multiple fields by splitting on a delimiter character or position.  
`spread`(ht, field, value[, key]) | Spread a key-value pair of fields across multiple fields.  



# Python API

This is the Python API documentation for all Hail Python libraries including
Query (hail), a cloud-agnostic file system implementation (hailtop.fs), and
Batch (hailtop.batch).

  * ``hail``
  * ``hailtop.fs``
  * ``hailtop.batch``

---

# Configuration Reference

Configuration variables can be set for Hail Query by:

  1. passing them as keyword arguments to ```init()```,

  2. running a command of the form `hailctl config set <VARIABLE_NAME> <VARIABLE_VALUE>` from the command line, or

  3. setting them as shell environment variables by running a command of the form `export <VARIABLE_NAME>=<VARIABLE_VALUE>` in a terminal, which will set the variable for the current terminal session.

Each method for setting configuration variables listed above overrides
variables set by any and all methods below it. For example, setting a
configuration variable by passing it to [`init()`](api.html#hail.init
"hail.init") will override any values set for the variable using either
`hailctl` or shell environment variables.

Warning

Some environment variables are shared between Hail Query and Hail Batch.
Setting one of these variables via ```init()```,
`hailctl`, or environment variables will affect both Query and Batch. However,
when instantiating a class specific to one of the two, passing configuration
to that class will not affect the other. For example, if one value for
`gcs_bucket_allow_list` is passed to [`init()`](api.html#hail.init
"hail.init"), a different value may be passed to the constructor for Batch’s
`ServiceBackend`, which will only affect that instance of the class (which can
only be used within Batch), and won’t affect Query.

## Supported Configuration Variables

GCS Bucket Allowlist Keyword Argument Name | `gcs_bucket_allow_list`  
---|---  
Keyword Argument Format | `["bucket1", "bucket2"]`  
`hailctl` Variable Name | `gcs/bucket_allow_list`  
Environment Variable Name | `HAIL_GCS_BUCKET_ALLOW_LIST`  
`hailctl` and Environment Variable Format | `bucket1,bucket2`  
Effect | Prevents Hail Query from erroring if the default storage policy for any of the given buckets is to use cold storage. Note: Only the default storage policy for the bucket is checked; individual objects in a bucket may be configured to use cold storage, even if the bucket is not. In the case of public access GCP buckets where the user does not have the appropriate permissions to check the default storage class of the bucket, the first object encountered in the bucket will have its storage class checked, and this will be assumed to be the default storage policy of the bucket.  
Shared between Query and Batch | Yes

---

# Cheat Sheets

Note

Hail’s cheat sheets are relatively new. We welcome suggestions for additional
cheatsheets, as well as feedback about our documentation. If you’d like to add
a cheatsheet to the documentation, make a pull request!

[Hail Tables Cheat Sheet](_static/cheatsheets/hail_tables_cheat_sheet.pdf)

[Hail MatrixTables Cheat
Sheet](_static/cheatsheets/hail_matrix_tables_cheat_sheet.pdf)

---

# Datasets

Warning

All functionality described on this page is experimental and subject to
change.

This page describes genetic datasets that are hosted in public buckets on both
Google Cloud Storage and Amazon S3. Note that these datasets are stored in
``Requester Pays`` buckets on GCS,
and are available in both the US-CENTRAL1 and EUROPE-WEST1 regions. On AWS,
the datasets are shared via [Open Data on
AWS](https://aws.amazon.com/opendata/) and are in buckets in the US region.

Check out the
[`load_dataset()`](experimental/index.html#hail.experimental.load_dataset
"hail.experimental.load_dataset") function to see how to load one of these
datasets into a Hail pipeline. You will need to provide the name, version, and
reference genome build of the desired dataset, as well as specify the region
your cluster is in and the cloud platform. Egress charges may apply if your
cluster is outside of the region specified.

Schemas for Available Datasets

  * ``Schemas``

Search

name | description | version | reference genome | cloud: [regions]  
---|---|---|---|---

---

# Libraries  
  
This pages lists any external libraries we are aware of that are built on top
of Hail. These libraries are not developed by the Hail team so we cannot
necessarily answer questions about them, but they may provide useful functions
not included in base Hail.

* * *

## gnomad (Hail Utilities for gnomAD)

This repo contains a number of Hail utility functions and scripts for the
[gnomAD](https://gnomad.broadinstitute.org) project and the [Translational
Genomics Group](https://the-tgg.org/).

Install with `pip install gnomad`.

More info can be found in the
[documentation](https://broadinstitute.github.io/gnomad_methods/) or on the
[PyPI project page](https://pypi.org/project/gnomad/).

---

# For Software Developers

Hail is an open-source project. We welcome contributions to the repository.

## Requirements

  * [Java 11 JDK](https://adoptopenjdk.net/index.html) . If you have a Mac, you must use a compatible architecture (`uname -m` prints your architecture).

  * The Python and non-pip installation requirements in ``Getting Started``. Note: These instructions install the JRE but that is not necessary as the JDK should already be installed which includes the JRE.

  * If you are setting HAIL_COMPILE_NATIVES=1, then you need the LZ4 library header files. On Debian and Ubuntu machines run: apt-get install liblz4-dev.

## Building Hail

The Hail source code is hosted [on GitHub](https://github.com/hail-is/hail):

    
    
    git clone https://github.com/hail-is/hail.git
    cd hail/hail
    

By default, Hail uses pre-compiled native libraries that are compatible with
recent Mac OS X and Debian releases. If you’re not using one of these OSes,
set the environment (or Make) variable HAIL_COMPILE_NATIVES to any value. This
variable tells GNU Make to build the native libraries from source.

Build and install a wheel file from source with local-mode `pyspark`:

    
    
    make install HAIL_COMPILE_NATIVES=1
    

As above, but explicitly specifying the Scala and Spark versions:

    
    
    make install HAIL_COMPILE_NATIVES=1 SCALA_VERSION=2.11.12 SPARK_VERSION=2.4.5
    

## Building the Docs and Website

Install build dependencies listed in the [docs style
guide](https://github.com/hail-is/hail/blob/main/hail/python/hail/docs/style-
guide.txt).

Build without rendering the notebooks (which is slow):

    
    
    make hail-docs-do-not-render-notebooks
    

Build while rendering the notebooks:

    
    
    make hail-docs
    

Serve the built website on <http://localhost:8000/>

    
    
    (cd build/www && python3 -m http.server)
    

## Running the tests

Install development dependencies:

    
    
    make -C .. install-dev-requirements
    

A couple Hail tests compare to PLINK 1.9 (not PLINK 2.0 [ignore the confusing
URL]):

>   * [PLINK 1.9](https://www.cog-genomics.org/plink2)
>
>

Execute every Hail test using at most 8 parallel threads:

    
    
    make -j8 test
    

# Import / Export

This page describes functionality for moving data in and out of Hail.

Hail has a suite of functionality for importing and exporting data to and from
general-purpose, genetics-specific, and high-performance native file formats.

## Native file formats

When saving data to disk with the intent to later use Hail, we highly
recommend that you use the native file formats to store
```Table``` and
```MatrixTable```
objects. These binary formats not only smaller than other formats (especially
textual ones) in most cases, but also are significantly faster to read into
Hail later.

These files can be created with methods on the
```Table``` and
```MatrixTable```
objects:

  * ```Table.write()```

  * ```MatrixTable.write()```

These files can be read into a Hail session later using the following methods:

`read_matrix_table`(path, *[, _intervals, ...]) | Read in a ```MatrixTable``` written with ```MatrixTable.write()```.  
---|---  
`read_table`(path, *[, _intervals, ...]) | Read in a ```Table``` written with ```Table.write()```.  
  
## Import

### General purpose

The `import_table()` function is widely-used to import textual data into a
Hail ```Table```.
`import_matrix_table()` is used to import two-dimensional matrix data in
textual representations into a Hail
```MatrixTable```.
Finally, it is possible to create a Hail Table from a
[`pandas`](https://pandas.pydata.org/docs/index.html#module-pandas "\(in
pandas v2.3.0\)") DataFrame with
[`Table.from_pandas()`](../hail.Table.html#hail.Table.from_pandas
"hail.Table.from_pandas").

`import_table`(paths[, key, min_partitions, ...]) | Import delimited text file (text table) as ```Table```.  
---|---  
`import_matrix_table`(paths[, row_fields, ...]) | Import tab-delimited file(s) as a ```MatrixTable```.  
`import_lines`(paths[, min_partitions, ...]) | Import lines of file(s) as a ```Table``` of strings.  
  
### Genetics

Hail has several functions to import genetics-specific file formats into Hail
```MatrixTable```
or ```Table``` objects:

`import_vcf`(path[, force, force_bgz, ...]) | Import VCF file(s) as a ```MatrixTable```.  
---|---  
`import_plink`(bed, bim, fam[, ...]) | Import a PLINK dataset (BED, BIM, FAM) as a ```MatrixTable```.  
`import_bed`(path[, reference_genome, ...]) | Import a UCSC BED file as a ```Table```.  
`import_bgen`(path, entry_fields[, ...]) | Import BGEN file(s) as a ```MatrixTable```.  
`index_bgen`(path[, index_file_map, ...]) | Index BGEN files as required by `import_bgen()`.  
`import_gen`(path[, sample_file, tolerance, ...]) | Import GEN file(s) as a ```MatrixTable```.  
`import_fam`(path[, quant_pheno, delimiter, ...]) | Import a PLINK FAM file into a ```Table```.  
`import_locus_intervals`(path[, ...]) | Import a locus interval list as a ```Table```.  
  
## Export

# Expression

_class
_hail.expr.Expression[[source]](_modules/hail/expr/expressions/base_expression.html#Expression)

    

Base class for Hail expressions.

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`collect` | Collect all records of an expression into a local list.  
---|---  
`describe` | Print information about type, index, and dependencies.  
`export` | Export a field to a text file.  
`show` | Print the first few records of the expression to the console.  
`summarize` | Compute and print summary information about the expression.  
`take` | Collect the first n records of an expression.  
  
__eq__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__eq__)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** (`Expression`) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__ge__)

    

Return self>=value.

__gt__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__gt__)

    

Return self>value.

__le__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__le__)

    

Return self<=value.

__lt__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__lt__)

    

Return self<value.

__ne__(_other_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.__ne__)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** (`Expression`) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize
=True_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.collect)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function
print>_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.describe)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header
=True_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.export)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols
=None_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.show)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler
=None_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.summarize)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize
=True_)[[source]](_modules/hail/expr/expressions/base_expression.html#Expression.take)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# BooleanExpression

_class
_hail.expr.BooleanExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression)

    

Expression of type [`tbool`](types.html#hail.expr.types.tbool
"hail.expr.types.tbool").

    
    
    >>> t = hl.literal(True)
    >>> f = hl.literal(False)
    >>> na = hl.missing(hl.tbool)
    
    
    
    >>> hl.eval(t)
    True
    
    
    
    >>> hl.eval(f)
    False
    
    
    
    >>> hl.eval(na)
    None
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__and__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression.__and__)

    

Return `True` if the left and right arguments are `True`.

Examples

    
    
    >>> hl.eval(t & f)
    False
    
    
    
    >>> hl.eval(t & na)
    None
    
    
    
    >>> hl.eval(f & na)
    False
    

The `&` and `|` operators have higher priority than comparison operators like
`==`, `<`, or `>`. Parentheses are often necessary:

    
    
    >>> x = hl.literal(5)
    
    
    
    >>> hl.eval((x < 10) & (x > 2))
    True
    

Parameters:

    

**other** (`BooleanExpression`) – Right-side operand.

Returns:

    

`BooleanExpression` – `True` if both left and right are `True`.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

`BooleanExpression` – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is greater than or equal to the
right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is greater than the right side.

__invert__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression.__invert__)

    

Return the boolean negation.

Examples

    
    
    >>> hl.eval(~t)
    False
    
    
    
    >>> hl.eval(~f)
    True
    
    
    
    >>> hl.eval(~na)
    None
    

Returns:

    

`BooleanExpression` – Boolean negation.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is smaller than or equal to the
right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

`BooleanExpression` – `True` if the left side is smaller than the right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

`BooleanExpression` – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__or__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#BooleanExpression.__or__)

    

Return `True` if at least one of the left and right arguments is `True`.

Examples

    
    
    >>> hl.eval(t | f)
    True
    
    
    
    >>> hl.eval(t | na)
    True
    
    
    
    >>> hl.eval(f | na)
    None
    

The `&` and `|` operators have higher priority than comparison operators like
`==`, `<`, or `>`. Parentheses are often necessary:

    
    
    >>> x = hl.literal(5)
    
    
    
    >>> hl.eval((x < 10) | (x > 20))
    True
    

Parameters:

    

**other** (`BooleanExpression`) – Right-side operand.

Returns:

    

`BooleanExpression` – `True` if either left or right is `True`.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# StructExpression

_class
_hail.expr.StructExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression)

    

Expression of type [`tstruct`](types.html#hail.expr.types.tstruct
"hail.expr.types.tstruct").

    
    
    >>> struct = hl.struct(a=5, b='Foo')
    

Struct fields are accessible as attributes and keys. It is therefore possible
to access field a of struct s with dot syntax:

    
    
    >>> hl.eval(struct.a)
    5
    

However, it is recommended to use square brackets to select fields:

    
    
    >>> hl.eval(struct['a'])
    5
    

The latter syntax is safer, because fields that share their name with an
existing attribute of `StructExpression` (keys, values, annotate, drop, etc.)
will only be accessible using the `StructExpression.__getitem__()` syntax.
This is also the only way to access fields that are not valid Python
identifiers, like fields with spaces or symbols.

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`annotate` | Add new fields or recompute existing fields.  
---|---  
`drop` | Drop fields from the struct.  
`flatten` | Recursively eliminate struct fields by adding their fields to this struct.  
`get` | See `StructExpression.__getitem__()`  
`items` | A list of pairs of field name and expression for said field.  
`keys` | The list of field names.  
`rename` | Rename fields of the struct.  
`select` | Select existing fields and compute new ones.  
`values` | A list of expressions for each field.  
  
__class_getitem___ = <bound method GenericAlias of <class
'hail.expr.expressions.typed_expressions.StructExpression'>>_

    

__eq__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.__eq__)

    

Check each field for equality.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – An expression of the same type.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.__getitem__)

    

Access a field of the struct by name or index.

Examples

    
    
    >>> hl.eval(struct['a'])
    5
    
    
    
    >>> hl.eval(struct[1])
    'Foo'
    

Parameters:

    

**item** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Field name.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Struct field.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.__ne__)

    

Return self!=value.

annotate(_**
named_exprs_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.annotate)

    

Add new fields or recompute existing fields.

Examples

    
    
    >>> hl.eval(struct.annotate(a=10, c=2*2*2))
    Struct(a=10, b='Foo', c=8)
    

Notes

If an expression in named_exprs shares a name with a field of the struct, then
that field will be replaced but keep its position in the struct. New fields
will be appended to the end of the struct.

Parameters:

    

**named_exprs** (keyword args of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Fields to add.

Returns:

    

`StructExpression` – Struct with new or updated fields.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

drop(_*
fields_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.drop)

    

Drop fields from the struct.

Examples

    
    
    >>> hl.eval(struct.drop('b'))
    Struct(a=5)
    

Parameters:

    

**fields** (varargs of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")) – Fields to drop.

Returns:

    

`StructExpression` – Struct without certain fields.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

flatten()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.flatten)

    

Recursively eliminate struct fields by adding their fields to this struct.

get(_k_ , _default
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.get)

    

See `StructExpression.__getitem__()`

items()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.items)

    

A list of pairs of field name and expression for said field.

keys()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.keys)

    

The list of field names.

rename(_mapping_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.rename)

    

Rename fields of the struct.

Examples

    
    
    >>> s = hl.struct(x='hello', y='goodbye', a='dogs')
    >>> s.rename({'x' : 'y', 'y' : 'z'}).show()
    +----------+----------+-----------+
    | <expr>.a | <expr>.y | <expr>.z  |
    +----------+----------+-----------+
    | str      | str      | str       |
    +----------+----------+-----------+
    | "dogs"   | "hello"  | "goodbye" |
    +----------+----------+-----------+
    

Parameters:

    

**mapping** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict
"\(in Python v3.9\)") of
[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)"), [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Mapping from old field names to new field names.

Notes

Any field that does not appear as a key in mapping will not be renamed.

Returns:

    

`StructExpression` – Struct with renamed fields.

select(_* fields_, _**
named_exprs_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.select)

    

Select existing fields and compute new ones.

Examples

    
    
    >>> hl.eval(struct.select('a', c=['bar', 'baz']))
    Struct(a=5, c=['bar', 'baz'])
    

Notes

The fields argument is a list of field names to keep. These fields will appear
in the resulting struct in the order they appear in fields.

The named_exprs arguments are new field expressions.

Parameters:

    

  * **fields** (varargs of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Field names to keep.

  * **named_exprs** (keyword args of ```Expression```) – New field expressions.

Returns:

    

`StructExpression` – Struct containing specified existing fields and computed
fields.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

values()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StructExpression.values)

    

A list of expressions for each field.

---

# Core language functions

`literal`(x[, dtype]) | Captures and broadcasts a Python variable or object as an expression.  
---|---  
`cond`(condition, consequent, alternate[, ...]) | Deprecated in favor of `if_else()`.  
`if_else`(condition, consequent, alternate[, ...]) | Expression for an if/else statement; tests a condition and returns one of two options based on the result.  
`switch`(expr) | Build a conditional tree on the value of an expression.  
`case`([missing_false]) | Chain multiple if-else statements with a ```CaseBuilder```.  
`bind`(f, *exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
`rbind`(*exprs[, _ctx]) | Bind a temporary variable and use it in a function.  
`missing`(t) | Creates an expression representing a missing value of a specified type.  
`null`(t) | Deprecated in favor of `missing()`.  
`str`(x) | Returns the string representation of x.  
`is_missing`(expression) | Returns `True` if the argument is missing.  
`is_defined`(expression) | Returns `True` if the argument is not missing.  
`coalesce`(*args) | Returns the first non-missing value of args.  
`or_else`(a, b) | If a is missing, return b.  
`or_missing`(predicate, value) | Returns value if predicate is `True`, otherwise returns missing.  
`range`(start[, stop, step]) | Returns an array of integers from start to stop by step.  
`query_table`(path, point_or_interval) | Query records from a table corresponding to a given point or range of keys.  
  
hail.expr.functions.literal(_x_ , _dtype
=None_)[[source]](../_modules/hail/expr/functions.html#literal)

    

Captures and broadcasts a Python variable or object as an expression.

Examples

    
    
    >>> table = hl.utils.range_table(8)
    >>> greetings = hl.literal({1: 'Good morning', 4: 'Good afternoon', 6 : 'Good evening'})
    >>> table.annotate(greeting = greetings.get(table.idx)).show()
    +-------+------------------+
    |   idx | greeting         |
    +-------+------------------+
    | int32 | str              |
    +-------+------------------+
    |     0 | NA               |
    |     1 | "Good morning"   |
    |     2 | NA               |
    |     3 | NA               |
    |     4 | "Good afternoon" |
    |     5 | NA               |
    |     6 | "Good evening"   |
    |     7 | NA               |
    +-------+------------------+
    

Notes

Use this function to capture large Python objects for use in expressions. This
function provides an alternative to adding an object as a global annotation on
a ```Table``` or
```MatrixTable```.

Parameters:

    

**x** – Object to capture and broadcast as an expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.cond(_condition_ , _consequent_ , _alternate_ ,
_missing_false =False_)[[source]](../_modules/hail/expr/functions.html#cond)

    

Deprecated in favor of `if_else()`.

Expression for an if/else statement; tests a condition and returns one of two
options based on the result.

Examples

    
    
    >>> x = 5
    >>> hl.eval(hl.cond(x < 2, 'Hi', 'Bye'))
    'Bye'
    
    
    
    >>> a = hl.literal([1, 2, 3, 4])
    >>> hl.eval(hl.cond(hl.len(a) > 0, 2.0 * a, a / 2.0))
    [2.0, 4.0, 6.0, 8.0]
    

Notes

If condition evaluates to `True`, returns consequent. If condition evaluates
to `False`, returns alternate. If predicate is missing, returns missing.

Note

The type of consequent and alternate must be the same.

Parameters:

    

  * **condition** (```BooleanExpression```) – Condition to test.

  * **consequent** (```Expression```) – Branch to return if the condition is `True`.

  * **alternate** (```Expression```) – Branch to return if the condition is `False`.

  * **missing_false** (```bool```) – If `True`, treat missing condition as `False`.

See also

`case()`, `switch()`, `if_else()`

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – One of consequent, alternate, or missing, based on
condition.

hail.expr.functions.if_else(_condition_ , _consequent_ , _alternate_ ,
_missing_false
=False_)[[source]](../_modules/hail/expr/functions.html#if_else)

    

Expression for an if/else statement; tests a condition and returns one of two
options based on the result.

Examples

    
    
    >>> x = 5
    >>> hl.eval(hl.if_else(x < 2, 'Hi', 'Bye'))
    'Bye'
    
    
    
    >>> a = hl.literal([1, 2, 3, 4])
    >>> hl.eval(hl.if_else(hl.len(a) > 0, 2.0 * a, a / 2.0))
    [2.0, 4.0, 6.0, 8.0]
    

Notes

If condition evaluates to `True`, returns consequent. If condition evaluates
to `False`, returns alternate. If predicate is missing, returns missing.

Note

The type of consequent and alternate must be the same.

Parameters:

    

  * **condition** (```BooleanExpression```) – Condition to test.

  * **consequent** (```Expression```) – Branch to return if the condition is `True`.

  * **alternate** (```Expression```) – Branch to return if the condition is `False`.

  * **missing_false** (```bool```) – If `True`, treat missing condition as `False`.

See also

`case()`, `switch()`

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – One of consequent, alternate, or missing, based on
condition.

hail.expr.functions.switch(_expr_)[[source]](../_modules/hail/expr/functions.html#switch)

    

Build a conditional tree on the value of an expression.

Examples

    
    
    >>> csq = hl.literal('loss of function')
    >>> expr = (hl.switch(csq)
    ...                  .when('synonymous', 1)
    ...                  .when('SYN', 1)
    ...                  .when('missense', 2)
    ...                  .when('MIS', 2)
    ...                  .when('loss of function', 3)
    ...                  .when('LOF', 3)
    ...                  .or_missing())
    >>> hl.eval(expr)
    3
    

See also

[`SwitchBuilder`](hail.expr.builders.SwitchBuilder.html#hail.expr.builders.SwitchBuilder
"hail.expr.builders.SwitchBuilder"), `case()`, `cond()`

Parameters:

    

**expr** ([`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value to match against.

Returns:

    

[`SwitchBuilder`](hail.expr.builders.SwitchBuilder.html#hail.expr.builders.SwitchBuilder
"hail.expr.builders.SwitchBuilder")

hail.expr.functions.case(_missing_false
=False_)[[source]](../_modules/hail/expr/functions.html#case)

    

Chain multiple if-else statements with a
[`CaseBuilder`](hail.expr.builders.CaseBuilder.html#hail.expr.builders.CaseBuilder
"hail.expr.builders.CaseBuilder").

Examples

    
    
    >>> x = hl.literal('foo bar baz')
    >>> expr = (hl.case()
    ...                  .when(x[:3] == 'FOO', 1)
    ...                  .when(hl.len(x) == 11, 2)
    ...                  .when(x == 'secret phrase', 3)
    ...                  .default(0))
    >>> hl.eval(expr)
    2
    

Parameters:

    

**missing_false** ([`bool`](constructors.html#hail.expr.functions.bool
"hail.expr.functions.bool")) – Treat missing predicates as `False`.

See also

[`CaseBuilder`](hail.expr.builders.CaseBuilder.html#hail.expr.builders.CaseBuilder
"hail.expr.builders.CaseBuilder"), `switch()`, `cond()`

Returns:

    

[`CaseBuilder`](hail.expr.builders.CaseBuilder.html#hail.expr.builders.CaseBuilder
"hail.expr.builders.CaseBuilder").

hail.expr.functions.bind(_f_ , _* exprs_, __ctx
=None_)[[source]](../_modules/hail/expr/functions.html#bind)

    

Bind a temporary variable and use it in a function.

Examples

    
    
    >>> hl.eval(hl.bind(lambda x: x + 1, 1))
    2
    

`bind()` also can take multiple arguments:

    
    
    >>> hl.eval(hl.bind(lambda x, y: x / y, x, x))
    1.0
    

Parameters:

    

  * **f** (function ( (args) -> ```Expression```)) – Function of exprs.

  * **exprs** (variable-length args of ```Expression```) – Expressions to bind.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Result of evaluating f with exprs as arguments.

hail.expr.functions.rbind(_* exprs_, __ctx
=None_)[[source]](../_modules/hail/expr/functions.html#rbind)

    

Bind a temporary variable and use it in a function.

This is `bind()` with flipped argument order.

Examples

    
    
    >>> hl.eval(hl.rbind(1, lambda x: x + 1))
    2
    

`rbind()` also can take multiple arguments:

    
    
    >>> hl.eval(hl.rbind(4.0, 2.0, lambda x, y: x / y))
    2.0
    

Parameters:

    

  * **exprs** (variable-length args of ```Expression```) – Expressions to bind.

  * **f** (function ( (args) -> ```Expression```)) – Function of exprs.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Result of evaluating f with exprs as arguments.

hail.expr.functions.missing(_t_)[[source]](../_modules/hail/expr/functions.html#missing)

    

Creates an expression representing a missing value of a specified type.

Examples

    
    
    >>> hl.eval(hl.missing(hl.tarray(hl.tstr)))
    None
    
    
    
    >>> hl.eval(hl.missing('array<str>'))
    None
    

Notes

This method is useful for constructing an expression that includes missing
values, since [`None`](https://docs.python.org/3.9/library/constants.html#None
"\(in Python v3.9\)") cannot be interpreted as an expression.

Parameters:

    

**t** (`str` or [`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the missing expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – A missing expression of type t.

hail.expr.functions.null(_t_)[[source]](../_modules/hail/expr/functions.html#null)

    

Deprecated in favor of `missing()`.

Creates an expression representing a missing value of a specified type.

Examples

    
    
    >>> hl.eval(hl.null(hl.tarray(hl.tstr)))
    None
    
    
    
    >>> hl.eval(hl.null('array<str>'))
    None
    

Notes

This method is useful for constructing an expression that includes missing
values, since [`None`](https://docs.python.org/3.9/library/constants.html#None
"\(in Python v3.9\)") cannot be interpreted as an expression.

Parameters:

    

**t** (`str` or [`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the missing expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – A missing expression of type t.

hail.expr.functions.str(_x_)[[source]](../_modules/hail/expr/functions.html#str)

    

Returns the string representation of x.

Examples

    
    
    >>> hl.eval(hl.str(hl.struct(a=5, b=7)))
    '{"a":5,"b":7}'
    

Parameters:

    

**x**

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

hail.expr.functions.is_missing(_expression_)[[source]](../_modules/hail/expr/functions.html#is_missing)

    

Returns `True` if the argument is missing.

Examples

    
    
    >>> hl.eval(hl.is_missing(5))
    False
    
    
    
    >>> hl.eval(hl.is_missing(hl.missing(hl.tstr)))
    True
    
    
    
    >>> hl.eval(hl.is_missing(hl.missing(hl.tbool) & True))
    True
    

Parameters:

    

**expression** – Expression to test.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if expression is missing, `False`
otherwise.

hail.expr.functions.is_defined(_expression_)[[source]](../_modules/hail/expr/functions.html#is_defined)

    

Returns `True` if the argument is not missing.

Examples

    
    
    >>> hl.eval(hl.is_defined(5))
    True
    
    
    
    >>> hl.eval(hl.is_defined(hl.missing(hl.tstr)))
    False
    
    
    
    >>> hl.eval(hl.is_defined(hl.missing(hl.tbool) & True))
    False
    

Parameters:

    

**expression** – Expression to test.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if expression is not missing, `False`
otherwise.

hail.expr.functions.coalesce(_*
args_)[[source]](../_modules/hail/expr/functions.html#coalesce)

    

Returns the first non-missing value of args.

Examples

    
    
    >>> x1 = hl.missing('int')
    >>> x2 = 2
    >>> hl.eval(hl.coalesce(x1, x2))
    2
    

Notes

All arguments must have the same type, or must be convertible to a common type
(all numeric, for instance).

See also

`or_else()`

Parameters:

    

**args** (variable-length args of
[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.or_else(_a_ ,
_b_)[[source]](../_modules/hail/expr/functions.html#or_else)

    

If a is missing, return b.

Examples

    
    
    >>> hl.eval(hl.or_else(5, 7))
    5
    
    
    
    >>> hl.eval(hl.or_else(hl.missing(hl.tint32), 7))
    7
    

See also

`coalesce()`

Parameters:

    

  * **a** (```Expression```)

  * **b** (```Expression```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.or_missing(_predicate_ ,
_value_)[[source]](../_modules/hail/expr/functions.html#or_missing)

    

Returns value if predicate is `True`, otherwise returns missing.

Examples

    
    
    >>> hl.eval(hl.or_missing(True, 5))
    5
    
    
    
    >>> hl.eval(hl.or_missing(False, 5))
    None
    

Parameters:

    

  * **predicate** (```BooleanExpression```)

  * **value** (```Expression```) – Value to return if predicate is `True`.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – This expression has the same type as b.

hail.expr.functions.range(_start_ , _stop =None_, _step
=1_)[[source]](../_modules/hail/expr/functions.html#range)

    

Returns an array of integers from start to stop by step.

Examples

    
    
    >>> hl.eval(hl.range(10))
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    
    
    
    >>> hl.eval(hl.range(3, 10))
    [3, 4, 5, 6, 7, 8, 9]
    
    
    
    >>> hl.eval(hl.range(0, 10, step=3))
    [0, 3, 6, 9]
    

Notes

The range includes start, but excludes stop.

If provided exactly one argument, the argument is interpreted as stop and
start is set to zero. This matches the behavior of Python’s `range`.

Parameters:

    

  * **start** (int or ```Expression``` of type ```tint32```) – Start of range.

  * **stop** (int or ```Expression``` of type ```tint32```) – End of range.

  * **step** (int or ```Expression``` of type ```tint32```) – Step of range.

Returns:

    

[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression")

hail.expr.functions.query_table(_path_ ,
_point_or_interval_)[[source]](../_modules/hail/expr/functions.html#query_table)

    

Query records from a table corresponding to a given point or range of keys.

Notes

This function does not dispatch to a distributed runtime; it can be used
inside already-distributed queries such as in
[`Table.annotate()`](../hail.Table.html#hail.Table.annotate
"hail.Table.annotate").

Warning

This function contains no safeguards against reading large amounts of data
using a single thread.

Parameters:

    

  * **path** (`str`) – Table path.

  * **point_or_interval** – Point or interval to query.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

```ConditionalBuilder``` | Base class for ```SwitchBuilder``` and ```CaseBuilder```  
---|---  
```CaseBuilder``` | Class for chaining multiple if-else statements.  
```SwitchBuilder``` | Class for generating conditional trees based on value of an expression.

---

# Constructor functions  
  
Constructors

`bool`(x) | Convert to a Boolean expression.  
---|---  
`float`(x) | Convert to a 64-bit floating point expression.  
`float32`(x) | Convert to a 32-bit floating point expression.  
`float64`(x) | Convert to a 64-bit floating point expression.  
`int`(x) | Convert to a 32-bit integer expression.  
`int32`(x) | Convert to a 32-bit integer expression.  
`int64`(x) | Convert to a 64-bit integer expression.  
`interval`(start, end[, includes_start, ...]) | Construct an interval expression.  
`struct`(**kwargs) | Construct a struct expression.  
`tuple`(iterable) | Construct a tuple expression.  
  
Collection constructors

`array`(collection) | Construct an array expression.  
---|---  
`empty_array`(t) | Returns an empty array of elements of a type t.  
`set`(collection) | Convert a set expression.  
`empty_set`(t) | Returns an empty set of elements of a type t.  
`dict`(collection) | Creates a dictionary.  
`empty_dict`(key_type, value_type) | Returns an empty dictionary with key type key_type and value type value_type.  
  
hail.expr.functions.bool(_x_)[[source]](../_modules/hail/expr/functions.html#bool)

    

Convert to a Boolean expression.

Examples

    
    
    >>> hl.eval(hl.bool('TRUE'))
    True
    
    
    
    >>> hl.eval(hl.bool(1.5))
    True
    

Notes

Numeric expressions return `True` if they are non-zero, and `False` if they
are zero.

Acceptable string values are: `'True'`, `'true'`, `'TRUE'`, `'False'`,
`'false'`, and `'FALSE'`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.float(_x_)[[source]](../_modules/hail/expr/functions.html#float)

    

Convert to a 64-bit floating point expression.

Examples

    
    
    >>> hl.eval(hl.float('1.1'))
    1.1
    
    
    
    >>> hl.eval(hl.float(1))
    1.0
    
    
    
    >>> hl.eval(hl.float(True))
    1.0
    

Note

Alias for `float64()`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.float32(_x_)[[source]](../_modules/hail/expr/functions.html#float32)

    

Convert to a 32-bit floating point expression.

Examples

    
    
    >>> hl.eval(hl.float32('1.1'))
    1.100000023841858
    
    
    
    >>> hl.eval(hl.float32(1))
    1.0
    
    
    
    >>> hl.eval(hl.float32(True))
    1.0
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
[`tfloat32`](../types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32")

hail.expr.functions.float64(_x_)[[source]](../_modules/hail/expr/functions.html#float64)

    

Convert to a 64-bit floating point expression.

Examples

    
    
    >>> hl.eval(hl.float64('1.1'))
    1.1
    
    
    
    >>> hl.eval(hl.float64(1))
    1.0
    
    
    
    >>> hl.eval(hl.float64(True))
    1.0
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.int(_x_)[[source]](../_modules/hail/expr/functions.html#int)

    

Convert to a 32-bit integer expression.

Examples

    
    
    >>> hl.eval(hl.int('1'))
    1
    
    
    
    >>> hl.eval(hl.int(1.5))
    1
    
    
    
    >>> hl.eval(hl.int(True))
    1
    

Note

Alias for `int32()`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
```tint32```

hail.expr.functions.int32(_x_)[[source]](../_modules/hail/expr/functions.html#int32)

    

Convert to a 32-bit integer expression.

Examples

    
    
    >>> hl.eval(hl.int32('1'))
    1
    
    
    
    >>> hl.eval(hl.int32(1.5))
    1
    
    
    
    >>> hl.eval(hl.int32(True))
    1
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
```tint32```

hail.expr.functions.int64(_x_)[[source]](../_modules/hail/expr/functions.html#int64)

    

Convert to a 64-bit integer expression.

Examples

    
    
    >>> hl.eval(hl.int64('1'))
    1
    
    
    
    >>> hl.eval(hl.int64(1.5))
    1
    
    
    
    >>> hl.eval(hl.int64(True))
    1
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or
[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") of type
```tint64```

hail.expr.functions.interval(_start_ , _end_ , _includes_start =True_,
_includes_end
=False_)[[source]](../_modules/hail/expr/functions.html#interval)

    

Construct an interval expression.

Examples

    
    
    >>> hl.eval(hl.interval(5, 100))
    Interval(start=5, end=100, includes_start=True, includes_end=False)
    
    
    
    >>> hl.eval(hl.interval(hl.locus("1", 100), hl.locus("1", 1000)))
        Interval(start=Locus(contig=1, position=100, reference_genome=GRCh37),
                 end=Locus(contig=1, position=1000, reference_genome=GRCh37),
                 includes_start=True,
                 includes_end=False)
    

Notes

start and end must have the same type.

Parameters:

    

  * **start** (```Expression```) – Start point.

  * **end** (```Expression```) – End point.

  * **includes_start** (```BooleanExpression```) – If `True`, interval includes start point.

  * **includes_end** (```BooleanExpression```) – If `True`, interval includes end point.

Returns:

    

[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

hail.expr.functions.struct(_**
kwargs_)[[source]](../_modules/hail/expr/functions.html#struct)

    

Construct a struct expression.

Examples

    
    
    >>> s = hl.struct(a=5, b='Foo')
    >>> hl.eval(s.a)
    5
    

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Keyword arguments as a struct.

hail.expr.functions.tuple(_iterable_)[[source]](../_modules/hail/expr/functions.html#tuple)

    

Construct a tuple expression.

Examples

    
    
    >>> t = hl.tuple([1, 2, '3'])
    >>> hl.eval(t)
    (1, 2, '3')
    
    
    
    >>> hl.eval(t[2])
    '3'
    

Parameters:

    

**iterable** (an iterable of
[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Tuple elements.

Returns:

    

[`TupleExpression`](../hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

hail.expr.functions.array(_collection_)[[source]](../_modules/hail/expr/functions.html#array)

    

Construct an array expression.

Examples

    
    
    >>> s = {'Bob', 'Charlie', 'Alice'}
    
    
    
    >>> hl.eval(hl.array(s))
    ['Alice', 'Bob', 'Charlie']
    

Parameters:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") or
[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"))

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.empty_array(_t_)[[source]](../_modules/hail/expr/functions.html#empty_array)

    

Returns an empty array of elements of a type t.

Examples

    
    
    >>> hl.eval(hl.empty_array(hl.tint32))
    []
    

Parameters:

    

**t** (```str``` or
[`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the array elements.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.set(_collection_)[[source]](../_modules/hail/expr/functions.html#set)

    

Convert a set expression.

Examples

    
    
    >>> s = hl.set(['Bob', 'Charlie', 'Alice', 'Bob', 'Bob'])
    >>> hl.eval(s) 
    {'Alice', 'Bob', 'Charlie'}
    

Returns:

    

[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Set of all unique elements.

hail.expr.functions.empty_set(_t_)[[source]](../_modules/hail/expr/functions.html#empty_set)

    

Returns an empty set of elements of a type t.

Examples

    
    
    >>> hl.eval(hl.empty_set(hl.tstr))
    set()
    

Parameters:

    

**t** (```str``` or
[`HailType`](../types.html#hail.expr.types.HailType
"hail.expr.types.HailType")) – Type of the set elements.

Returns:

    

[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")

hail.expr.functions.dict(_collection_)[[source]](../_modules/hail/expr/functions.html#dict)

    

Creates a dictionary.

Examples

    
    
    >>> hl.eval(hl.dict([('foo', 1), ('bar', 2), ('baz', 3)]))
    {'bar': 2, 'baz': 3, 'foo': 1}
    

Notes

This method expects arrays or sets with elements of type
```ttuple``` with
2 fields. The first field of the tuple becomes the key, and the second field
becomes the value.

Parameters:

    

**collection**
([`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") or
[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"))

Returns:

    

[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression")

hail.expr.functions.empty_dict(_key_type_ ,
_value_type_)[[source]](../_modules/hail/expr/functions.html#empty_dict)

    

Returns an empty dictionary with key type key_type and value type value_type.

Examples

    
    
    >>> hl.eval(hl.empty_dict(hl.tstr, hl.tint32))
    {}
    

Parameters:

    

  * **key_type** (```str``` or ```HailType```) – Type of the keys.

  * **value_type** (```str``` or ```HailType```) – Type of the values.

Returns:

    

[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression")

---

# ArrayExpression

_class
_hail.expr.ArrayExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression)

    

Expression of type [`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray").

    
    
    >>> names = hl.literal(['Alice', 'Bob', 'Charlie'])
    

See also

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`aggregate` | Uses the aggregator library to compute a summary from an array.  
---|---  
`append` | Append an element to the array and return the result.  
`contains` | Returns a boolean indicating whether item is found in the array.  
`extend` | Concatenate two arrays and return the result.  
`first` | Returns the first element of the array, or missing if empty.  
`grouped` | Partition an array into fixed size subarrays.  
`head` | Deprecated in favor of `first()`.  
`index` | Returns the first index of x, or missing.  
`last` | Returns the last element of the array, or missing if empty.  
`scan` | Map each element of the array to cumulative value of function f, with initial value zero.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.__getitem__)

    

Index into or slice the array.

Examples

Index with a single integer:

    
    
    >>> hl.eval(names[1])
    'Bob'
    
    
    
    >>> hl.eval(names[-1])
    'Charlie'
    

Slicing is also supported:

    
    
    >>> hl.eval(names[1:])
    ['Bob', 'Charlie']
    

Parameters:

    

**item** (slice or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Index or slice.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element or array slice.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

aggregate(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.aggregate)

    

Uses the aggregator library to compute a summary from an array.

This method is useful for accessing functionality that exists in the
aggregator library but not the basic expression library, for instance,
[`call_stats()`](aggregators.html#hail.expr.aggregators.call_stats
"hail.expr.aggregators.call_stats").

Parameters:

    

**f** – Aggregation function

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

all(_f_)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

append(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.append)

    

Append an element to the array and return the result.

Examples

    
    
    >>> hl.eval(names.append('Dan'))
    ['Alice', 'Bob', 'Charlie', 'Dan']
    

Note

This method does not mutate the caller, but instead returns a new array by
copying the caller and adding item.

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Element to append, same type as the array element
type.

Returns:

    

`ArrayExpression`

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.contains)

    

Returns a boolean indicating whether item is found in the array.

Examples

    
    
    >>> hl.eval(names.contains('Charlie'))
    True
    
    
    
    >>> hl.eval(names.contains('Helen'))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Item for inclusion test.

Warning

This method takes time proportional to the length of the array. If a pipeline
uses this method on the same array several times, it may be more efficient to
convert the array to a set first early in the script
([`set()`](functions/constructors.html#hail.expr.functions.set
"hail.expr.functions.set")).

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the element is found in the array,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

extend(_a_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.extend)

    

Concatenate two arrays and return the result.

Examples

    
    
    >>> hl.eval(names.extend(['Dan', 'Edith']))
    ['Alice', 'Bob', 'Charlie', 'Dan', 'Edith']
    

Parameters:

    

**a** (`ArrayExpression`) – Array to concatenate, same type as the callee.

Returns:

    

`ArrayExpression`

filter(_f_)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an `ArrayExpression`, returns an
`ArrayExpression`.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression") – Expression of the same type as the callee.

find(_f_)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

first()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.first)

    

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.first())
    'Alice'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).first()) None

flatmap(_f_)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) ->
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"))) – Function from the element type of the
collection to the type of the collection. For instance, flatmap on a
`set<str>` should take a `str` and return a `set`.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

fold(_f_ , _zero_)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

grouped(_group_size_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.grouped)

    

Partition an array into fixed size subarrays.

Examples

    
    
    >>> a = hl.array([0, 1, 2, 3, 4])
    
    
    
    >>> hl.eval(a.grouped(2))
    [[0, 1], [2, 3], [4]]
    

Parameters:

    

**group_size**
([`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")) – The number of elements per group.

Returns:

    

`ArrayExpression`.

head()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.head)

    

Deprecated in favor of `first()`.

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.head())
    'Alice'
    

If the array has no elements, then the result is missing:

    
    
    >>> hl.eval(names.filter(lambda x: x.startswith('D')).head())
    None
    

index(_x_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.index)

    

Returns the first index of x, or missing.

Parameters:

    

**x** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") or
[`typing.Callable`](https://docs.python.org/3.9/library/typing.html#typing.Callable
"\(in Python v3.9\)")) – Value to find, or function from element to Boolean
expression.

Returns:

    

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

Examples

    
    
    >>> hl.eval(names.index('Bob'))
    1
    
    
    
    >>> hl.eval(names.index('Beth'))
    None
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('e')))
    0
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('h')))
    None
    

last()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.last)

    

Returns the last element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.last())
    'Charlie'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).last()) None

length()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

scan(_f_ ,
_zero_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayExpression.scan)

    

Map each element of the array to cumulative value of function f, with initial
value zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.array_scan(lambda i, j: i + j, 0, a))
    [0, 0, 1, 3]
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

`ArrayExpression`.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# ArrayNumericExpression

_class
_hail.expr.ArrayNumericExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression)

    

Expression of type [`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray") with a numeric type.

Numeric arrays support arithmetic both with scalar values and other arrays.
Arithmetic between two numeric arrays requires that the length of each array
is identical, and will apply the operation positionally (`a1 * a2` will
multiply the first element of `a1` by the first element of `a2`, the second
element of `a1` by the second element of `a2`, and so on). Arithmetic with a
scalar will apply the operation to each element of the array.

    
    
    >>> a1 = hl.literal([0, 1, 2, 3, 4, 5])
    
    
    
    >>> a2 = hl.literal([1, -1, 1, -1, 1, -1])
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__add__)

    

Positionally add an array or a scalar.

Examples

    
    
    >>> hl.eval(a1 + 5)
    [5, 6, 7, 8, 9, 10]
    
    
    
    >>> hl.eval(a1 + a2)
    [1, 0, 3, 2, 5, 4]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to add.

Returns:

    

`ArrayNumericExpression` – Array of positional sums.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__floordiv__)

    

Positionally divide by an array or a scalar using floor division.

Examples

    
    
    >>> hl.eval(a1 // 2)
    [0, 0, 1, 1, 2, 2]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`)

Returns:

    

`ArrayNumericExpression`

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)

    

Index into or slice the array.

Examples

Index with a single integer:

    
    
    >>> hl.eval(names[1])
    'Bob'
    
    
    
    >>> hl.eval(names[-1])
    'Charlie'
    

Slicing is also supported:

    
    
    >>> hl.eval(names[1:])
    ['Bob', 'Charlie']
    

Parameters:

    

**item** (slice or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Index or slice.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element or array slice.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__mod__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__mod__)

    

Positionally compute the left modulo the right.

Examples

    
    
    >>> hl.eval(a1 % 2)
    [0, 1, 0, 1, 0, 1]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`)

Returns:

    

`ArrayNumericExpression`

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__mul__)

    

Positionally multiply by an array or a scalar.

Examples

    
    
    >>> hl.eval(a2 * 5)
    [5, -5, 5, -5, 5, -5]
    
    
    
    >>> hl.eval(a1 * a2)
    [0, -1, 2, -3, 4, -5]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to multiply by.

Returns:

    

`ArrayNumericExpression` – Array of positional products.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__neg__)

    

Negate elements of the array.

Examples

    
    
    >>> hl.eval(-a1)
    [0, -1, -2, -3, -4, -5]
    

Returns:

    

`ArrayNumericExpression` – Array expression of the same type.

__pow__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__pow__)

    

Positionally raise to the power of an array or a scalar.

Examples

    
    
    >>> hl.eval(a1 ** 2)
    [0.0, 1.0, 4.0, 9.0, 16.0, 25.0]
    
    
    
    >>> hl.eval(a1 ** a2)
    [0.0, 1.0, 2.0, 0.3333333333333333, 4.0, 0.2]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`)

Returns:

    

`ArrayNumericExpression`

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__sub__)

    

Positionally subtract an array or a scalar.

Examples

    
    
    >>> hl.eval(a2 - 1)
    [0, -2, 0, -2, 0, -2]
    
    
    
    >>> hl.eval(a1 - a2)
    [-1, 2, 1, 4, 3, 6]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to subtract.

Returns:

    

`ArrayNumericExpression` – Array of positional differences.

__truediv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#ArrayNumericExpression.__truediv__)

    

Positionally divide by an array or a scalar.

Examples

    
    
    >>> hl.eval(a1 / 10)  
    [0.0, 0.1, 0.2, 0.3, 0.4, 0.5]
    
    
    
    >>> hl.eval(a2 / a1)  
    [inf, -1.0, 0.5, -0.3333333333333333, 0.25, -0.2]
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `ArrayNumericExpression`) – Value or array
to divide by.

Returns:

    

`ArrayNumericExpression` – Array of positional quotients.

aggregate(_f_)

    

Uses the aggregator library to compute a summary from an array.

This method is useful for accessing functionality that exists in the
aggregator library but not the basic expression library, for instance,
[`call_stats()`](aggregators.html#hail.expr.aggregators.call_stats
"hail.expr.aggregators.call_stats").

Parameters:

    

**f** – Aggregation function

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

all(_f_)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

append(_item_)

    

Append an element to the array and return the result.

Examples

    
    
    >>> hl.eval(names.append('Dan'))
    ['Alice', 'Bob', 'Charlie', 'Dan']
    

Note

This method does not mutate the caller, but instead returns a new array by
copying the caller and adding item.

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Element to append, same type as the array element
type.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)

    

Returns a boolean indicating whether item is found in the array.

Examples

    
    
    >>> hl.eval(names.contains('Charlie'))
    True
    
    
    
    >>> hl.eval(names.contains('Helen'))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Item for inclusion test.

Warning

This method takes time proportional to the length of the array. If a pipeline
uses this method on the same array several times, it may be more efficient to
convert the array to a set first early in the script
([`set()`](functions/constructors.html#hail.expr.functions.set
"hail.expr.functions.set")).

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the element is found in the array,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

extend(_a_)

    

Concatenate two arrays and return the result.

Examples

    
    
    >>> hl.eval(names.extend(['Dan', 'Edith']))
    ['Alice', 'Bob', 'Charlie', 'Dan', 'Edith']
    

Parameters:

    

**a**
([`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")) – Array to concatenate, same type as the callee.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

filter(_f_)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression") – Expression of the same type as the callee.

find(_f_)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

first()

    

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.first())
    'Alice'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).first()) None

flatmap(_f_)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) ->
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"))) – Function from the element type of the
collection to the type of the collection. For instance, flatmap on a
`set<str>` should take a `str` and return a `set`.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

fold(_f_ , _zero_)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

grouped(_group_size_)

    

Partition an array into fixed size subarrays.

Examples

    
    
    >>> a = hl.array([0, 1, 2, 3, 4])
    
    
    
    >>> hl.eval(a.grouped(2))
    [[0, 1], [2, 3], [4]]
    

Parameters:

    

**group_size**
([`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")) – The number of elements per group.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

head()

    

Deprecated in favor of
[`first()`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression.first
"hail.expr.ArrayExpression.first").

Returns the first element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.head())
    'Alice'
    

If the array has no elements, then the result is missing:

    
    
    >>> hl.eval(names.filter(lambda x: x.startswith('D')).head())
    None
    

index(_x_)

    

Returns the first index of x, or missing.

Parameters:

    

**x** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") or
[`typing.Callable`](https://docs.python.org/3.9/library/typing.html#typing.Callable
"\(in Python v3.9\)")) – Value to find, or function from element to Boolean
expression.

Returns:

    

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

Examples

    
    
    >>> hl.eval(names.index('Bob'))
    1
    
    
    
    >>> hl.eval(names.index('Beth'))
    None
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('e')))
    0
    
    
    
    >>> hl.eval(names.index(lambda x: x.endswith('h')))
    None
    

last()

    

Returns the last element of the array, or missing if empty.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Element.

Examples

    
    
    >>> hl.eval(names.last())
    'Charlie'
    

If the array has no elements, then the result is missing: >>>
hl.eval(names.filter(lambda x: x.startswith(‘D’)).last()) None

length()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

scan(_f_ , _zero_)

    

Map each element of the array to cumulative value of function f, with initial
value zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.array_scan(lambda i, j: i + j, 0, a))
    [0, 0, 1, 3]
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# CallExpression

_class
_hail.expr.CallExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression)

    

Expression of type [`tcall`](types.html#hail.expr.types.tcall
"hail.expr.types.tcall").

    
    
    >>> call = hl.call(0, 1, phased=False)
    

Attributes

`dtype` | The data type of the expression.  
---|---  
`phased` | True if the call is phased.  
`ploidy` | Return the number of alleles of this call.  
  
Methods

`contains_allele` | Returns true if the call has one or more called alleles of the given index.  
---|---  
`is_diploid` | True if the call has ploidy equal to 2.  
`is_haploid` | True if the call has ploidy equal to 1.  
`is_het` | Evaluate whether the call includes two different alleles.  
`is_het_non_ref` | Evaluate whether the call includes two different alleles, neither of which is reference.  
`is_het_ref` | Evaluate whether the call includes two different alleles, one of which is reference.  
`is_hom_ref` | Evaluate whether the call includes two reference alleles.  
`is_hom_var` | Evaluate whether the call includes two identical alternate alleles.  
`is_non_ref` | Evaluate whether the call includes one or more non-reference alleles.  
`n_alt_alleles` | Returns the number of non-reference alleles.  
`one_hot_alleles` | Returns an array containing the summed one-hot encoding of the alleles.  
`unphase` | Returns an unphased version of this call.  
`unphased_diploid_gt_index` | Return the genotype index for unphased, diploid calls.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.__getitem__)

    

Get the i*th* allele.

Examples

Index with a single integer:

    
    
    >>> hl.eval(call[0])
    0
    
    
    
    >>> hl.eval(call[1])
    1
    

Parameters:

    

**item** (int or [`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Allele index.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains_allele(_allele_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.contains_allele)

    

Returns true if the call has one or more called alleles of the given index.

    
    
    >>> c = hl.call(0, 3)
    
    
    
    >>> hl.eval(c.contains_allele(3))
    True
    
    
    
    >>> hl.eval(c.contains_allele(1))
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

is_diploid()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_diploid)

    

True if the call has ploidy equal to 2.

Examples

    
    
    >>> hl.eval(call.is_diploid())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

is_haploid()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_haploid)

    

True if the call has ploidy equal to 1.

Examples

    
    
    >>> hl.eval(call.is_haploid())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

is_het()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_het)

    

Evaluate whether the call includes two different alleles.

Examples

    
    
    >>> hl.eval(call.is_het())
    True
    

Notes

In the diploid biallelic case, a `0/1` call will return `True`, and `0/0` and
`1/1` will return `False`.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two alleles are different,
`False` if they are the same.

is_het_non_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_het_non_ref)

    

Evaluate whether the call includes two different alleles, neither of which is
reference.

Examples

    
    
    >>> hl.eval(call.is_het_non_ref())
    False
    

Notes

A biallelic variant may never have a het-non-ref call. Examples of these calls
are `1/2` and `2/4`.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes two different
alternate alleles, `False` otherwise.

is_het_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_het_ref)

    

Evaluate whether the call includes two different alleles, one of which is
reference.

Examples

    
    
    >>> hl.eval(call.is_het_ref())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes one reference and
one alternate allele, `False` otherwise.

is_hom_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_hom_ref)

    

Evaluate whether the call includes two reference alleles.

Examples

    
    
    >>> hl.eval(call.is_hom_ref())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes two reference
alleles, `False` otherwise.

is_hom_var()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_hom_var)

    

Evaluate whether the call includes two identical alternate alleles.

Examples

    
    
    >>> hl.eval(call.is_hom_var())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the call includes two identical
alternate alleles, `False` otherwise.

is_non_ref()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.is_non_ref)

    

Evaluate whether the call includes one or more non-reference alleles.

Examples

    
    
    >>> hl.eval(call.is_non_ref())
    True
    

Notes

In the diploid biallelic case, a `0/0` call will return `False`, and `0/1` and
`1/1` will return `True`.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if at least one allele is non-
reference, `False` otherwise.

n_alt_alleles()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.n_alt_alleles)

    

Returns the number of non-reference alleles.

Examples

    
    
    >>> hl.eval(call.n_alt_alleles())
    1
    

Notes

For diploid biallelic calls, this method is equivalent to the alternate allele
dosage. For instance, `0/0` will return `0`, `0/1` will return `1`, and `1/1`
will return `2`.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of non-reference alleles.

one_hot_alleles(_alleles_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.one_hot_alleles)

    

Returns an array containing the summed one-hot encoding of the alleles.

Examples

Compute one-hot encoding when number of total alleles is 2.

    
    
    >>> hl.eval(call.one_hot_alleles(2))
    [1, 1]
    

**DEPRECATED** : Compute one-hot encoding based on length of list of alleles.

    
    
    >>> hl.eval(call.one_hot_alleles(['A', 'T']))
    [1, 1]
    

This one-hot representation is the positional sum of the one-hot encoding for
each called allele. For a biallelic variant, the one-hot encoding for a
reference allele is `[1, 0]` and the one-hot encoding for an alternate allele
is `[0, 1]`. Diploid calls would produce the following arrays: `[2, 0]` for
homozygous reference, `[1, 1]` for heterozygous, and `[0, 2]` for homozygous
alternate.

Parameters:

    

**alleles**
([`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") of [`tstr`](types.html#hail.expr.types.tstr
"hail.expr.types.tstr").) – Number of total alleles, including the reference,
or array of variant alleles.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") of [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – An array of summed one-hot encodings of allele
indices.

_property _phased

    

True if the call is phased.

Examples

    
    
    >>> hl.eval(call.phased)
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

_property _ploidy

    

Return the number of alleles of this call.

Examples

    
    
    >>> hl.eval(call.ploidy)
    2
    

Notes

Currently only ploidy 1 and 2 are supported.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

unphase()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.unphase)

    

Returns an unphased version of this call.

Returns:

    

`CallExpression`

unphased_diploid_gt_index()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CallExpression.unphased_diploid_gt_index)

    

Return the genotype index for unphased, diploid calls.

Examples

    
    
    >>> hl.eval(call.unphased_diploid_gt_index())
    1
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")

---

# CollectionExpression

_class
_hail.expr.CollectionExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression)

    

Expression of type [`tarray`](types.html#hail.expr.types.tarray
"hail.expr.types.tarray") or [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset")

    
    
    >>> a = hl.literal([1, 2, 3, 4, 5])
    
    
    
    >>> s3 = hl.literal({'Alice', 'Bob', 'Charlie'})
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`all` | Returns `True` if f returns `True` for every element.  
---|---  
`any` | Returns `True` if f returns `True` for any element.  
`filter` | Returns a new collection containing elements where f returns `True`.  
`find` | Returns the first element where f returns `True`.  
`flatmap` | Map each element of the collection to a new collection, and flatten the results.  
`fold` | Reduces the collection with the given function f, provided the initial value zero.  
`group_by` | Group elements into a dict according to a lambda function.  
`length` | Returns the size of a collection.  
`map` | Transform each element of a collection.  
`size` | Returns the size of a collection.  
`starmap` | Transform each element of a collection of tuples.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

all(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.all)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.any)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

filter(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.filter)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

`CollectionExpression` – Expression of the same type as the callee.

find(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.find)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

flatmap(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.flatmap)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) -> `CollectionExpression`)) – Function from the
element type of the collection to the type of the collection. For instance,
flatmap on a `set<str>` should take a `str` and return a `set`.

Returns:

    

`CollectionExpression`

fold(_f_ ,
_zero_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.fold)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.group_by)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

length()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.length)

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.map)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

`CollectionExpression`. – Collection where each element has been transformed
according to f.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.size)

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#CollectionExpression.starmap)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

`CollectionExpression`. – Collection where each element has been transformed
according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# DictExpression

_class
_hail.expr.DictExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression)

    

Expression of type [`tdict`](types.html#hail.expr.types.tdict
"hail.expr.types.tdict").

    
    
    >>> d = hl.literal({'Alice': 43, 'Bob': 33, 'Charles': 44})
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`contains` | Returns whether a given key is present in the dictionary.  
---|---  
`get` | Returns the value associated with key k or a default value if that key is not present.  
`items` | Returns an array of tuples containing key/value pairs in the dictionary.  
`key_set` | Returns the set of keys in the dictionary.  
`keys` | Returns an array with all keys in the dictionary.  
`map_values` | Transform values of the dictionary according to a function.  
`size` | Returns the size of the dictionary.  
`values` | Returns an array with all values in the dictionary.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.__getitem__)

    

Get the value associated with key item.

Examples

    
    
    >>> hl.eval(d['Alice'])
    43
    

Notes

Raises an error if item is not a key of the dictionary. Use
`DictExpression.get()` to return missing instead of an error.

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Key expression.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Value associated with key item.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.contains)

    

Returns whether a given key is present in the dictionary.

Examples

    
    
    >>> hl.eval(d.contains('Alice'))
    True
    
    
    
    >>> hl.eval(d.contains('Anne'))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Key to test for inclusion.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if item is a key of the dictionary,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

get(_item_ , _default
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.get)

    

Returns the value associated with key k or a default value if that key is not
present.

Examples

    
    
    >>> hl.eval(d.get('Alice'))
    43
    
    
    
    >>> hl.eval(d.get('Anne'))
    None
    
    
    
    >>> hl.eval(d.get('Anne', 0))
    0
    

Parameters:

    

  * **item** (```Expression```) – Key.

  * **default** (```Expression```) – Default value. Must be same type as dictionary values.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – The value associated with item, or default.

items()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.items)

    

Returns an array of tuples containing key/value pairs in the dictionary.

Examples

    
    
    >>> hl.eval(d.items())  
    [('Alice', 430), ('Bob', 330), ('Charles', 440)]
    

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – All key/value pairs in the dictionary.

key_set()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.key_set)

    

Returns the set of keys in the dictionary.

Examples

    
    
    >>> hl.eval(d.key_set())  
    {'Alice', 'Bob', 'Charles'}
    

Returns:

    

[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Set of all keys.

keys()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.keys)

    

Returns an array with all keys in the dictionary.

Examples

    
    
    >>> hl.eval(d.keys())  
    ['Bob', 'Charles', 'Alice']
    

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of all keys.

map_values(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.map_values)

    

Transform values of the dictionary according to a function.

Examples

    
    
    >>> hl.eval(d.map_values(lambda x: x * 10))  
    {'Alice': 430, 'Bob': 330, 'Charles': 440}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to apply to each value.

Returns:

    

`DictExpression` – Dictionary with transformed values.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.size)

    

Returns the size of the dictionary.

Examples

    
    
    >>> hl.eval(d.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – Size of the dictionary.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

values()[[source]](_modules/hail/expr/expressions/typed_expressions.html#DictExpression.values)

    

Returns an array with all values in the dictionary.

Examples

    
    
    >>> hl.eval(d.values())  
    [33, 44, 43]
    

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – All values in the dictionary.

---

# IntervalExpression

_class
_hail.expr.IntervalExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#IntervalExpression)

    

Expression of type [`tinterval`](types.html#hail.expr.types.tinterval
"hail.expr.types.tinterval").

    
    
    >>> interval = hl.interval(3, 11)
    >>> locus_interval = hl.parse_locus_interval("1:53242-90543")
    

Attributes

`dtype` | The data type of the expression.  
---|---  
`end` | Returns the end point.  
`includes_end` | True if the interval includes the end point.  
`includes_start` | True if the interval includes the start point.  
`start` | Returns the start point.  
  
Methods

`contains` | Tests whether a value is contained in the interval.  
---|---  
`overlaps` | True if the the supplied interval contains any value in common with this one.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_value_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#IntervalExpression.contains)

    

Tests whether a value is contained in the interval.

Examples

    
    
    >>> hl.eval(interval.contains(3))
    True
    
    
    
    >>> hl.eval(interval.contains(11))
    False
    

Parameters:

    

**value** – Object with type matching the interval point type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if value is contained in the interval,
`False` otherwise.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

_property _end

    

Returns the end point.

Examples

    
    
    >>> hl.eval(interval.end)
    11
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

_property _includes_end

    

True if the interval includes the end point.

Examples

    
    
    >>> hl.eval(interval.includes_end)
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

_property _includes_start

    

True if the interval includes the start point.

Examples

    
    
    >>> hl.eval(interval.includes_start)
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

overlaps(_interval_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#IntervalExpression.overlaps)

    

True if the the supplied interval contains any value in common with this one.

Examples

    
    
    >>> hl.eval(interval.overlaps(hl.interval(5, 9)))
    True
    
    
    
    >>> hl.eval(interval.overlaps(hl.interval(11, 20)))
    False
    

Parameters:

    

**interval** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") with type
[`tinterval`](types.html#hail.expr.types.tinterval
"hail.expr.types.tinterval")) – Interval object with the same point type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

_property _start

    

Returns the start point.

Examples

    
    
    >>> hl.eval(interval.start)
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# LocusExpression

_class
_hail.expr.LocusExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression)

    

Expression of type [`tlocus`](types.html#hail.expr.types.tlocus
"hail.expr.types.tlocus").

    
    
    >>> locus = hl.locus('1', 1034245)
    

Attributes

`contig` | Returns the chromosome.  
---|---  
`contig_idx` | Returns the chromosome.  
`dtype` | The data type of the expression.  
`position` | Returns the position along the chromosome.  
  
Methods

`global_position` | Returns a zero-indexed absolute position along the reference genome.  
---|---  
`in_autosome` | Returns `True` if the locus is on an autosome.  
`in_autosome_or_par` | Returns `True` if the locus is on an autosome or a pseudoautosomal region of chromosome X or Y.  
`in_mito` | Returns `True` if the locus is on mitochondrial DNA.  
`in_x_nonpar` | Returns `True` if the locus is in a non-pseudoautosomal region of chromosome X.  
`in_x_par` | Returns `True` if the locus is in a pseudoautosomal region of chromosome X.  
`in_y_nonpar` | Returns `True` if the locus is in a non-pseudoautosomal region of chromosome Y.  
`in_y_par` | Returns `True` if the locus is in a pseudoautosomal region of chromosome Y.  
`sequence_context` | Return the reference genome sequence at the locus.  
`window` | Returns an interval of a specified number of bases around the locus.  
  
__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

_property _contig

    

Returns the chromosome.

Examples

    
    
    >>> hl.eval(locus.contig)
    '1'
    

Returns:

    

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression") – The chromosome for this locus.

_property _contig_idx

    

Returns the chromosome.

Examples

    
    
    >>> hl.eval(locus.contig_idx)
    0
    

Returns:

    

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression") – The index of the chromosome for this locus.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

global_position()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.global_position)

    

Returns a zero-indexed absolute position along the reference genome.

The global position is computed as `position` \- 1 plus the sum of the lengths
of all the contigs that precede this locus’s `contig` in the reference
genome’s ordering of contigs.

See also
[`locus_from_global_position()`](functions/genetics.html#hail.expr.functions.locus_from_global_position
"hail.expr.functions.locus_from_global_position").

Examples

A locus with position 1 along chromosome 1 will have a global position of 0
along the reference genome GRCh37.

    
    
    >>> hl.eval(hl.locus('1', 1).global_position())
    0
    

A locus with position 1 along chromosome 2 will have a global position of
(1-1) + 249250621, where 249250621 is the length of chromosome 1 on GRCh37.

    
    
    >>> hl.eval(hl.locus('2', 1).global_position())
    249250621
    

A different reference genome than the default results in a different global
position.

    
    
    >>> hl.eval(hl.locus('chr2', 1, 'GRCh38').global_position())
    248956422
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") – Global base position of locus along the reference
genome.

in_autosome()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_autosome)

    

Returns `True` if the locus is on an autosome.

Notes

All contigs are considered autosomal except those designated as X, Y, or MT by
[`ReferenceGenome`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome").

Examples

    
    
    >>> hl.eval(locus.in_autosome())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_autosome_or_par()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_autosome_or_par)

    

Returns `True` if the locus is on an autosome or a pseudoautosomal region of
chromosome X or Y.

Examples

    
    
    >>> hl.eval(locus.in_autosome_or_par())
    True
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_mito()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_mito)

    

Returns `True` if the locus is on mitochondrial DNA.

Examples

    
    
    >>> hl.eval(locus.in_mito())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_x_nonpar()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_x_nonpar)

    

Returns `True` if the locus is in a non-pseudoautosomal region of chromosome
X.

Examples

    
    
    >>> hl.eval(locus.in_x_nonpar())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_x_par()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_x_par)

    

Returns `True` if the locus is in a pseudoautosomal region of chromosome X.

Examples

    
    
    >>> hl.eval(locus.in_x_par())
    False
    

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_y_nonpar()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_y_nonpar)

    

Returns `True` if the locus is in a non-pseudoautosomal region of chromosome
Y.

Examples

    
    
    >>> hl.eval(locus.in_y_nonpar())
    False
    

Note

Many variant callers only generate variants on chromosome X for the
pseudoautosomal region. In this case, all loci mapped to chromosome Y are non-
pseudoautosomal.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

in_y_par()[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.in_y_par)

    

Returns `True` if the locus is in a pseudoautosomal region of chromosome Y.

Examples

    
    
    >>> hl.eval(locus.in_y_par())
    False
    

Note

Many variant callers only generate variants on chromosome X for the
pseudoautosomal region. In this case, all loci mapped to chromosome Y are non-
pseudoautosomal.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

_property _position

    

Returns the position along the chromosome.

Examples

    
    
    >>> hl.eval(locus.position)
    1034245
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – This locus’s position along its chromosome.

sequence_context(_before =0_, _after
=0_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.sequence_context)

    

Return the reference genome sequence at the locus.

Examples

Get the reference allele at a locus:

    
    
    >>> hl.eval(locus.sequence_context()) 
    "G"
    

Get the reference sequence at a locus including the previous 5 bases:

    
    
    >>> hl.eval(locus.sequence_context(before=5)) 
    "ACTCGG"
    

Notes

This function requires that this locus’ reference genome has an attached
reference sequence. Use
[`ReferenceGenome.add_sequence()`](genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome.add_sequence
"hail.genetics.ReferenceGenome.add_sequence") to load and attach a reference
sequence to a reference genome.

Parameters:

    

  * **before** (```Expression``` of type ```tint32```, optional) – Number of bases to include before the locus. Truncates at contig boundary.

  * **after** (```Expression``` of type ```tint32```, optional) – Number of bases to include after the locus. Truncates at contig boundary.

Returns:

    

[`StringExpression`](hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

window(_before_ ,
_after_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#LocusExpression.window)

    

Returns an interval of a specified number of bases around the locus.

Examples

Create a window of two megabases centered at a locus:

    
    
    >>> locus = hl.locus('16', 29_500_000)
    >>> window = locus.window(1_000_000, 1_000_000)
    >>> hl.eval(window)
    Interval(start=Locus(contig=16, position=28500000, reference_genome=GRCh37), end=Locus(contig=16, position=30500000, reference_genome=GRCh37), includes_start=True, includes_end=True)
    

Notes

The returned interval is inclusive of both the start and end endpoints.

Parameters:

    

  * **before** (```Expression``` of type ```tint32```) – Number of bases to include before the locus. Truncates at 1.

  * **after** (```Expression``` of type ```tint32```) – Number of bases to include after the locus. Truncates at contig length.

Returns:

    

[`IntervalExpression`](hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

---

# NumericExpression

_class
_hail.expr.NumericExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression)

    

Expression of numeric type.

    
    
    >>> x = hl.literal(3)
    
    
    
    >>> y = hl.literal(4.5)
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__add__)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other** (`NumericExpression`) – Number to add.

Returns:

    

`NumericExpression` – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__floordiv__)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other** (`NumericExpression`) – Dividend.

Returns:

    

`NumericExpression` – The floor of the left number divided by the right.

__ge__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__ge__)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__gt__)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__le__)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__lt__)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other** (`NumericExpression`) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__mod__)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other** (`NumericExpression`) – Dividend.

Returns:

    

`NumericExpression` – Remainder after dividing the left by the right.

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__mul__)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other** (`NumericExpression`) – Number to multiply.

Returns:

    

`NumericExpression` – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__neg__)

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

`NumericExpression` – Negated number.

__pow__(_power_ , _modulo
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__pow__)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (`NumericExpression`)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__sub__)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other** (`NumericExpression`) – Number to subtract.

Returns:

    

`NumericExpression` – Difference of the two numbers.

__truediv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NumericExpression.__truediv__)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other** (`NumericExpression`) – Dividend.

Returns:

    

`NumericExpression` – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# Int32Expression

_class
_hail.expr.Int32Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Int32Expression)

    

Expression of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#Int32Expression.__mul__)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# Int64Expression

_class
_hail.expr.Int64Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Int64Expression)

    

Expression of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# Float32Expression

_class
_hail.expr.Float32Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Float32Expression)

    

Expression of type [`tfloat32`](types.html#hail.expr.types.tfloat32
"hail.expr.types.tfloat32").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# Float64Expression

_class
_hail.expr.Float64Expression[[source]](_modules/hail/expr/expressions/typed_expressions.html#Float64Expression)

    

Expression of type [`tfloat64`](types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64").

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

__add__(_other_)

    

Add two numbers.

Examples

    
    
    >>> hl.eval(x + 2)
    5
    
    
    
    >>> hl.eval(x + y)
    7.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to add.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Sum of the two numbers.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)

    

Divide two numbers with floor division.

Examples

    
    
    >>> hl.eval(x // 2)
    1
    
    
    
    >>> hl.eval(y // 2)
    2.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The floor of the left number divided by the
right.

__ge__(_other_)

    

Greater-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(y >= 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than or
equal to the right side.

__gt__(_other_)

    

Greater-than comparison.

Examples

    
    
    >>> hl.eval(y > 4)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is greater than the
right side.

__le__(_other_)

    

Less-than-or-equals comparison.

Examples

    
    
    >>> hl.eval(x <= 3)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than or
equal to the right side.

__lt__(_other_)

    

Less-than comparison.

Examples

    
    
    >>> hl.eval(x < 5)
    True
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Right side for comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the left side is smaller than the
right side.

__mod__(_other_)

    

Compute the left modulo the right number.

Examples

    
    
    >>> hl.eval(32 % x)
    2
    
    
    
    >>> hl.eval(7 % y)
    2.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Remainder after dividing the left by the
right.

__mul__(_other_)

    

Multiply two numbers.

Examples

    
    
    >>> hl.eval(x * 2)
    6
    
    
    
    >>> hl.eval(x * y)
    13.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to multiply.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Product of the two numbers.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()

    

Negate the number (multiply by -1).

Examples

    
    
    >>> hl.eval(-x)
    -3
    

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Negated number.

__pow__(_power_ , _modulo =None_)

    

Raise the left to the right power.

Examples

    
    
    >>> hl.eval(x ** 2)
    9.0
    
    
    
    >>> hl.eval(x ** -2)
    0.1111111111111111
    
    
    
    >>> hl.eval(y ** 1.5)
    9.545941546018392
    

Parameters:

    

  * **power** (```NumericExpression```)

  * **modulo** – Unsupported argument.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tfloat64``` –
Result of raising left to the right power.

__sub__(_other_)

    

Subtract the right number from the left.

Examples

    
    
    >>> hl.eval(x - 2)
    1
    
    
    
    >>> hl.eval(x - y)
    -1.5
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Number to subtract.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – Difference of the two numbers.

__truediv__(_other_)

    

Divide two numbers.

Examples

    
    
    >>> hl.eval(x / 2)
    1.5
    
    
    
    >>> hl.eval(y / 0.1)
    45.0
    

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")) – Dividend.

Returns:

    

[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") – The left number divided by the left.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# SetExpression

_class
_hail.expr.SetExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression)

    

Expression of type [`tset`](types.html#hail.expr.types.tset
"hail.expr.types.tset").

    
    
    >>> s1 = hl.literal({1, 2, 3})
    >>> s2 = hl.literal({1, 3, 5})
    

See also

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`add` | Returns a new set including item.  
---|---  
`contains` | Returns `True` if item is in the set.  
`difference` | Return the set of elements in the set that are not present in set s.  
`intersection` | Return the intersection of the set and set s.  
`is_subset` | Returns `True` if every element is contained in set s.  
`remove` | Returns a new set excluding item.  
`union` | Return the union of the set and set s.  
  
__and__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__and__)

    

Return the intersection of the set and other.

Examples

    
    
    >>> hl.eval(s1 & s2)
    {1, 3}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in both the set and other.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__ge__)

    

Test whether every element in other is in the set.

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if every element in other is in the
set. `False` otherwise.

__gt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__gt__)

    

Test whether other is a proper subset of the set (`other <= set and other !=
set`).

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if other is a proper subset of the
set. `False` otherwise.

__le__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__le__)

    

Test whether every element in the set is in other.

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if every element in the set is in
other. `False` otherwise.

__lt__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__lt__)

    

Test whether the set is a proper subset of other (`set <= other and set !=
other`).

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the set is a proper subset of
other. `False` otherwise.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__or__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__or__)

    

Return the union of the set and other.

Examples

    
    
    >>> hl.eval(s1 | s2)
    {1, 2, 3, 5}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in either set.

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__sub__)

    

Return the difference of the set and other.

Examples

    
    
    >>> hl.eval(s1 - s2)
    {2}
    
    
    
    >>> hl.eval(s2 - s1)
    {5}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements in the set that are not in other.

__xor__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.__xor__)

    

Return the symmetric difference of the set and other.

Examples

    
    
    >>> hl.eval(s1 ^ s2)
    {2, 5}
    

Parameters:

    

**other** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in either the set or other but not
both.

add(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.add)

    

Returns a new set including item.

Examples

    
    
    >>> hl.eval(s1.add(10))  
    {1, 2, 3, 10}
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value to add.

Returns:

    

`SetExpression` – Set with item added.

all(_f_)

    

Returns `True` if f returns `True` for every element.

Examples

    
    
    >>> hl.eval(a.all(lambda x: x < 10))
    True
    

Notes

This method returns `True` if the collection is empty.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for every
element, `False` otherwise.

any(_f_)

    

Returns `True` if f returns `True` for any element.

Examples

    
    
    >>> hl.eval(a.any(lambda x: x % 2 == 0))
    True
    
    
    
    >>> hl.eval(s3.any(lambda x: x[0] == 'D'))
    False
    

Notes

This method always returns `False` for empty collections.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"). – `True` if f returns `True` for any element,
`False` otherwise.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.contains)

    

Returns `True` if item is in the set.

Examples

    
    
    >>> hl.eval(s1.contains(1))
    True
    
    
    
    >>> hl.eval(s1.contains(10))
    False
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value for inclusion test.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if item is in the set.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

difference(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.difference)

    

Return the set of elements in the set that are not present in set s.

Examples

    
    
    >>> hl.eval(s1.difference(s2))
    {2}
    
    
    
    >>> hl.eval(s2.difference(s1))
    {5}
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements not in s.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

filter(_f_)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> hl.eval(a.filter(lambda x: x % 2 == 0))
    [2, 4]
    
    
    
    >>> hl.eval(s3.filter(lambda x: ~(x[-1] == 'e')))  
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a `SetExpression`, returns a
`SetExpression`. Evaluated on an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression") – Expression of the same type as the callee.

find(_f_)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> hl.eval(a.find(lambda x: x ** 2 > 20))
    5
    
    
    
    >>> hl.eval(s3.find(lambda x: x[0] == 'D'))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Parameters:

    

**f** (function ( (arg) ->
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression"))) – Function to evaluate for each element of
the collection. Must return a
[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression").

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

flatmap(_f_)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> hl.eval(a.flatmap(lambda x: hl.range(0, x)))
    [0, 0, 1, 0, 1, 2, 0, 1, 2, 3, 0, 1, 2, 3, 4]
    
    
    
    >>> hl.eval(s3.flatmap(lambda x: hl.set(hl.range(0, x.length()).map(lambda i: x[i]))))  
    {'A', 'B', 'C', 'a', 'b', 'c', 'e', 'h', 'i', 'l', 'o', 'r'}
    

Parameters:

    

**f** (function ( (arg) ->
[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"))) – Function from the element type of the
collection to the type of the collection. For instance, flatmap on a
`set<str>` should take a `str` and return a `set`.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression")

fold(_f_ , _zero_)

    

Reduces the collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression").

group_by(_f_)

    

Group elements into a dict according to a lambda function.

Examples

    
    
    >>> hl.eval(a.group_by(lambda x: x % 2 == 0))  
    {False: [1, 3, 5], True: [2, 4]}
    
    
    
    >>> hl.eval(s3.group_by(lambda x: x.length()))  
    {3: {'Bob'}, 5: {'Alice'}, 7: {'Charlie'}}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to evaluate for each element of the
collection to produce a key for the resulting dictionary.

Returns:

    

[`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

intersection(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.intersection)

    

Return the intersection of the set and set s.

Examples

    
    
    >>> hl.eval(s1.intersection(s2))
    {1, 3}
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in s.

is_subset(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.is_subset)

    

Returns `True` if every element is contained in set s.

Examples

    
    
    >>> hl.eval(s1.is_subset(s2))
    False
    
    
    
    >>> hl.eval(s1.remove(2).is_subset(s2))
    True
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if every element is contained in set
s.

length()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.length())
    5
    
    
    
    >>> hl.eval(s3.length())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

map(_f_)

    

Transform each element of a collection.

Examples

    
    
    >>> hl.eval(a.map(lambda x: x ** 3))
    [1.0, 8.0, 27.0, 64.0, 125.0]
    
    
    
    >>> hl.eval(s3.map(lambda x: x.length()))
    {3, 5, 7}
    

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

remove(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.remove)

    

Returns a new set excluding item.

Examples

    
    
    >>> hl.eval(s1.remove(1))
    {2, 3}
    

Parameters:

    

**item** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Value to remove.

Returns:

    

`SetExpression` – Set with item removed.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

size()

    

Returns the size of a collection.

Examples

    
    
    >>> hl.eval(a.size())
    5
    
    
    
    >>> hl.eval(s3.size())
    3
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – The number of elements in the collection.

starmap(_f_)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> hl.eval(hl.array([(1, 2), (2, 3)]).starmap(lambda x, y: x+y))
    [3, 5]
    

Parameters:

    

**f** (function ( (*args) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the
collection.

Returns:

    

[`CollectionExpression`](hail.expr.CollectionExpression.html#hail.expr.CollectionExpression
"hail.expr.CollectionExpression"). – Collection where each element has been
transformed according to f.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

union(_s_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#SetExpression.union)

    

Return the union of the set and set s.

Examples

    
    
    >>> hl.eval(s1.union(s2))
    {1, 2, 3, 5}
    

Parameters:

    

**s** (`SetExpression`) – Set expression of the same type.

Returns:

    

`SetExpression` – Set of elements present in either set.

---

# StringExpression

_class
_hail.expr.StringExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression)

    

Expression of type [`tstr`](types.html#hail.expr.types.tstr
"hail.expr.types.tstr").

    
    
    >>> s = hl.literal('The quick brown fox')
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`contains` | Returns whether substr is contained in the string.  
---|---  
`endswith` | Returns whether substr is a suffix of the string.  
`find` | Return the lowest index in the string where substring sub is found within the slice s[start:end].  
`first_match_in` | Returns an array containing the capture groups of the first match of regex in the given character sequence.  
`join` | Returns a string which is the concatenation of the strings in collection separated by the string providing this method.  
`length` | Returns the length of the string.  
`lower` | Returns a copy of the string, but with upper case letters converted to lower case.  
`matches` | Returns `True` if the string contains any match for the given regex if full_match is false.  
`replace` | Replace substrings matching pattern1 with pattern2 using regex.  
`reverse` | Returns the reversed value.  
`split` | Returns an array of strings generated by splitting the string at delim.  
`startswith` | Returns whether substr is a prefix of the string.  
`strip` | Returns a copy of the string with whitespace removed from the start and end.  
`translate` | Translates characters of the string using mapping.  
`upper` | Returns a copy of the string, but with lower case letters converted to upper case.  
  
__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.__add__)

    

Concatenate strings.

Examples

    
    
    >>> hl.eval(s + ' jumped over the lazy dog')
    'The quick brown fox jumped over the lazy dog'
    

Parameters:

    

**other** (`StringExpression`) – String to concatenate.

Returns:

    

`StringExpression` – Concatenated string.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.__getitem__)

    

Slice or index into the string.

Examples

    
    
    >>> hl.eval(s[:15])
    'The quick brown'
    
    
    
    >>> hl.eval(s[0])
    'T'
    

Parameters:

    

**item** (slice or
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32")) – Slice or character index.

Returns:

    

`StringExpression` – Substring or character at index item.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

contains(_substr_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.contains)

    

Returns whether substr is contained in the string.

Examples

    
    
    >>> hl.eval(s.contains('fox'))
    True
    
    
    
    >>> hl.eval(s.contains('dog'))
    False
    

Note

This method is case-sensitive.

Parameters:

    

**substr** (`StringExpression`)

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

endswith(_substr_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.endswith)

    

Returns whether substr is a suffix of the string.

Examples

    
    
    >>> hl.eval(s.endswith('fox'))
    True
    

Note

This method is case-sensitive.

Parameters:

    

**substr** (`StringExpression`)

Returns:

    

`StringExpression`

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

find(_sub_ , _start =None_, _end
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.find)

    

Return the lowest index in the string where substring sub is found within the
slice s[start:end]. Optional arguments start and end are interpreted as in
slice notation. Evaluates to -1 if sub is not found.

Examples

    
    
    >>> a = hl.str('hello, world')
    >>> hl.eval(a.find('world'))
    7
    
    
    
    >>> hl.eval(a.find('hail'))
    -1
    

Parameters:

    

  * **sub** (`StringExpression`) – substring to find

  * **start** (```Int32Expression```) – optional slice start index

  * **end** (```Int32Expression```) – optional slice end index

Returns:

    

[`Int32Expression`](hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") – lowest index in the string where substring sub
is found or -1.

first_match_in(_regex_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.first_match_in)

    

Returns an array containing the capture groups of the first match of regex in
the given character sequence.

Examples

    
    
    >>> hl.eval(s.first_match_in("The quick (\w+) fox"))
    ['brown']
    
    
    
    >>> hl.eval(s.first_match_in("The (\w+) (\w+) (\w+)"))
    ['quick', 'brown', 'fox']
    
    
    
    >>> hl.eval(s.first_match_in("(\w+) (\w+)"))
    ['The', 'quick']
    

Parameters:

    

**regex** (`StringExpression`)

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") with element type
```tstr```

join(_collection_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.join)

    

Returns a string which is the concatenation of the strings in collection
separated by the string providing this method. Raises
[`TypeError`](https://docs.python.org/3.9/library/exceptions.html#TypeError
"\(in Python v3.9\)") if the element type of collection is not
```tstr```.

Examples

    
    
    >>> a = ['Bob', 'Charlie', 'Alice', 'Bob', 'Bob']
    
    
    
    >>> hl.eval(hl.str(',').join(a))
    'Bob,Charlie,Alice,Bob,Bob'
    

Parameters:

    

**collection**
([`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")) – Collection.

Returns:

    

`StringExpression` – Joined string expression.

length()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.length)

    

Returns the length of the string.

Examples

    
    
    >>> hl.eval(s.length())
    19
    

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint32`](types.html#hail.expr.types.tint32
"hail.expr.types.tint32") – Length of the string.

lower()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.lower)

    

Returns a copy of the string, but with upper case letters converted to lower
case.

Examples

    
    
    >>> hl.eval(s.lower())
    'the quick brown fox'
    

Returns:

    

`StringExpression`

matches(_regex_ , _full_match
=False_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.matches)

    

Returns `True` if the string contains any match for the given regex if
full_match is false. Returns `True` if the whole string matches the given
regex if full_match is true.

Examples

The regex parameter does not need to match the entire string if full_match is
`False`:

    
    
    >>> string = hl.literal('NA12878')
    >>> hl.eval(string.matches('12'))
    True
    

The regex parameter needs to match the entire string if full_match is `True`:

    
    
    >>> string = hl.literal('NA12878')
    >>> hl.eval(string.matches('12', True))
    False
    
    
    
    >>> string = hl.literal('3412878')
    >>> hl.eval(string.matches('^[0-9]*$'))
    True
    

Regex motifs can be used to match sequences of characters:

    
    
    >>> string = hl.literal('NA12878')
    >>> hl.eval(string.matches(r'NA\d+'))
    True
    
    
    
    >>> string = hl.literal('3412878')
    >>> hl.eval(string.matches('^[0-9]*$'))
    True
    

Notes

The regex argument is a [regular
expression](https://en.wikipedia.org/wiki/Regular_expression), and uses [Java
regex
syntax](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html).

Parameters:

    

  * **regex** (`StringExpression`) – Pattern to match.

  * **full_match** (:obj: bool) – If `True`, the function considers whether the whole string matches the regex. If `False`, the function considers whether the string has a partial match for that regex

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – If full_match is `False`,``True`` if the
string contains any match for the regex, otherwise `False`. If full_match is
`True`,``True`` if the whole string matches the regex, otherwise `False`.

replace(_pattern1_ ,
_pattern2_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.replace)

    

Replace substrings matching pattern1 with pattern2 using regex.

Examples

Replace spaces with underscores in a Hail string:

    
    
    >>> hl.eval(hl.str("The quick  brown fox").replace(' ', '_'))
    'The_quick__brown_fox'
    

Remove the leading zero in contigs in variant strings in a table:

    
    
    >>> t = hl.import_table('data/leading-zero-variants.txt')
    >>> t.show()
    +----------------+
    | variant        |
    +----------------+
    | str            |
    +----------------+
    | "01:1000:A:T"  |
    | "01:10001:T:G" |
    | "02:99:A:C"    |
    | "02:893:G:C"   |
    | "22:100:A:T"   |
    | "X:10:C:A"     |
    +----------------+
    
    >>> t = t.annotate(variant = t.variant.replace("^0([0-9])", "$1"))
    >>> t.show()
    +---------------+
    | variant       |
    +---------------+
    | str           |
    +---------------+
    | "1:1000:A:T"  |
    | "1:10001:T:G" |
    | "2:99:A:C"    |
    | "2:893:G:C"   |
    | "22:100:A:T"  |
    | "X:10:C:A"    |
    +---------------+
    

Notes

The regex expressions used should follow [Java regex
syntax](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html).
In the Java regular expression syntax, a dollar sign, `$1`, refers to the
first group, not the canonical `\1`.

Parameters:

    

  * **pattern1** (str or `StringExpression`)

  * **pattern2** (str or `StringExpression`)

reverse()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.reverse)

    

Returns the reversed value. .. rubric:: Examples

    
    
    >>> string = hl.literal('ATGCC')
    >>> hl.eval(string.reverse())
    'CCGTA'
    

Returns:

    

`StringExpression`

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

split(_delim_ , _n
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.split)

    

Returns an array of strings generated by splitting the string at delim.

Examples

    
    
    >>> hl.eval(s.split('\s+'))
    ['The', 'quick', 'brown', 'fox']
    
    
    
    >>> hl.eval(s.split('\s+', 2))
    ['The', 'quick brown fox']
    

Notes

The delimiter is a regex using the [Java regex
syntax](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/regex/Pattern.html)
delimiter. To split on special characters, escape them with double backslash
(`\\`).

Parameters:

    

  * **delim** (str or `StringExpression`) – Delimiter regex.

  * **n** (```Expression``` of type ```tint32```, optional) – Maximum number of splits.

Returns:

    

[`ArrayExpression`](hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of split strings.

startswith(_substr_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.startswith)

    

Returns whether substr is a prefix of the string.

Examples

    
    
    >>> hl.eval(s.startswith('The'))
    True
    
    
    
    >>> hl.eval(s.startswith('the'))
    False
    

Note

This method is case-sensitive.

Parameters:

    

**substr** (`StringExpression`)

Returns:

    

`StringExpression`

strip()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.strip)

    

Returns a copy of the string with whitespace removed from the start and end.

Examples

    
    
    >>> s2 = hl.str('  once upon a time\n')
    >>> hl.eval(s2.strip())
    'once upon a time'
    

Returns:

    

`StringExpression`

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

translate(_mapping_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.translate)

    

Translates characters of the string using mapping.

Examples

    
    
    >>> string = hl.literal('ATTTGCA')
    >>> hl.eval(string.translate({'T': 'U'}))
    'AUUUGCA'
    

Parameters:

    

**mapping**
([`DictExpression`](hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression")) – Dictionary of character-character translations.

Returns:

    

`StringExpression`

See also

`replace()`

upper()[[source]](_modules/hail/expr/expressions/typed_expressions.html#StringExpression.upper)

    

Returns a copy of the string, but with lower case letters converted to upper
case.

Examples

    
    
    >>> hl.eval(s.upper())
    'THE QUICK BROWN FOX'
    

Returns:

    

`StringExpression`

---

# TupleExpression

_class
_hail.expr.TupleExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression)

    

Expression of type [`ttuple`](types.html#hail.expr.types.ttuple
"hail.expr.types.ttuple").

    
    
    >>> tup = hl.literal(("a", 1, [1, 2, 3]))
    

Attributes

`dtype` | The data type of the expression.  
---|---  
  
Methods

`count` | Do not use this method.  
---|---  
`index` | Do not use this method.  
  
__class_getitem___ = <bound method GenericAlias of <class
'hail.expr.expressions.typed_expressions.TupleExpression'>>_

    

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__getitem__(_item_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.__getitem__)

    

Index into the tuple.

Examples

    
    
    >>> hl.eval(tup[1])
    1
    

Parameters:

    

**item** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)")) – Element index.

Returns:

    

[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__len__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.__len__)

    

Returns the length of the tuple.

Examples

    
    
    >>> len(tup)
    3
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

count(_value_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.count)

    

Do not use this method.

This only exists for compatibility with the Python Sequence abstract base
class.

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

index(_value_ , _start =0_, _stop
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#TupleExpression.index)

    

Do not use this method.

This only exists for compatibility with the Python Sequence abstract base
class.

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

---

# NDArrayExpression

_class
_hail.expr.NDArrayExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression)

    

Expression of type [`tndarray`](types.html#hail.expr.types.tndarray
"hail.expr.types.tndarray").

    
    
    >>> nd = hl.nd.array([[1, 2], [3, 4]])
    

Attributes

`T` | Reverse the dimensions of this ndarray.  
---|---  
`dtype` | The data type of the expression.  
`ndim` | The number of dimensions of this ndarray.  
`shape` | The shape of this ndarray.  
  
Methods

`map` | Applies an element-wise operation on an NDArray.  
---|---  
`map2` | Applies an element-wise binary operation on two NDArrays.  
`reshape` | Reshape this ndarray to a new shape.  
`transpose` | Permute the dimensions of this ndarray according to the ordering of axes.  
  
_property _T

    

Reverse the dimensions of this ndarray. For an n-dimensional array a, a[i_0,
…, i_n-1, i_n] = a.T[i_n, i_n-1, …, i_0]. Same as self.transpose().

See also `transpose()`.

Returns:

    

`NDArrayExpression`.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

map(_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.map)

    

Applies an element-wise operation on an NDArray.

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the NDArray.

Returns:

    

`NDArrayExpression`. – NDArray where each element has been transformed
according to f.

map2(_other_ ,
_f_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.map2)

    

Applies an element-wise binary operation on two NDArrays.

Parameters:

    

  * **other** (class:.NDArrayExpression, ```ArrayExpression```, numpy NDarray,) – or nested python list/tuples. Both NDArrays must be the same shape or broadcastable into common shape.

  * **f** (function ((arg1, arg2)-> ```Expression```)) – Function to be applied to each element of both NDArrays.

Returns:

    

`NDArrayExpression`. – Element-wise result of applying f to each index in
NDArrays.

_property _ndim

    

The number of dimensions of this ndarray.

Examples

    
    
    >>> nd.ndim
    2
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

reshape(_*
shape_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.reshape)

    

Reshape this ndarray to a new shape.

Parameters:

    

**shape** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or) – :obj: tuple of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64")

Examples

    
    
    >>> v = hl.nd.array([1, 2, 3, 4]) 
    >>> m = v.reshape((2, 2)) 
    

Returns:

    

`NDArrayExpression`.

_property _shape

    

The shape of this ndarray.

Examples

    
    
    >>> hl.eval(nd.shape)
    (2, 2)
    

Returns:

    

[`TupleExpression`](hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

transpose(_axes
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayExpression.transpose)

    

Permute the dimensions of this ndarray according to the ordering of axes. Axis
j in the ith index of axes maps the jth dimension of the ndarray to the ith
dimension of the output ndarray.

Parameters:

    

**axes** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple
"\(in Python v3.9\)") of
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)"), optional) – The new ordering of the ndarray’s dimensions.

Notes

Does nothing on ndarrays of dimensionality 0 or 1.

Returns:

    

`NDArrayExpression`.

---

# NDArrayNumericExpression

_class
_hail.expr.NDArrayNumericExpression[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression)

    

Expression of type [`tndarray`](types.html#hail.expr.types.tndarray
"hail.expr.types.tndarray") with a numeric element type.

Numeric ndarrays support arithmetic both with scalar values and other arrays.
Arithmetic between two numeric ndarrays requires that the shapes of each
ndarray be either identical or compatible for broadcasting. Operations are
applied positionally (`nd1 * nd2` will multiply the first element of `nd1` by
the first element of `nd2`, the second element of `nd1` by the second element
of `nd2`, and so on). Arithmetic with a scalar will apply the operation to
each element of the ndarray.

Attributes

`T` | Reverse the dimensions of this ndarray.  
---|---  
`dtype` | The data type of the expression.  
`ndim` | The number of dimensions of this ndarray.  
`shape` | The shape of this ndarray.  
  
Methods

`sum` | Sum out one or more axes of an ndarray.  
---|---  
  
_property _T

    

Reverse the dimensions of this ndarray. For an n-dimensional array a, a[i_0,
…, i_n-1, i_n] = a.T[i_n, i_n-1, …, i_0]. Same as self.transpose().

See also `transpose()`.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression").

__add__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__add__)

    

Positionally add an array or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to add.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional sums.

__eq__(_other_)

    

Returns `True` if the two expressions are equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x == y)
    True
    
    
    
    >>> hl.eval(x == z)
    False
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for equality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are equal.

__floordiv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__floordiv__)

    

Positionally divide by a ndarray or a scalar using floor division.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`)

Returns:

    

`NDArrayNumericExpression`

__ge__(_other_)

    

Return self>=value.

__gt__(_other_)

    

Return self>value.

__le__(_other_)

    

Return self<=value.

__lt__(_other_)

    

Return self<value.

__matmul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__matmul__)

    

Matrix multiplication: a @ b, semantically equivalent to NumPy matmul. If a
and b are vectors, the vector dot product is performed, returning a
NumericExpression. If a and b are both 2-dimensional matrices, this performs
normal matrix multiplication. If a and b have more than 2 dimensions, they are
treated as multi-dimensional stacks of 2-dimensional matrices. Matrix
multiplication is applied element-wise across the higher dimensions. E.g. if a
has shape (3, 4, 5) and b has shape (3, 5, 6), a is treated as a stack of
three matrices of shape (4, 5) and b as a stack of three matrices of shape (5,
6). a @ b would then have shape (3, 4, 6).

Notes

The last dimension of a and the second to last dimension of b (or only
dimension if b is a vector) must have the same length. The dimensions to the
left of the last two dimensions of a and b (for NDArrays of dimensionality >
2) must be equal or be compatible for broadcasting. Number of dimensions of
both NDArrays must be at least 1.

Parameters:

    

**other**
([`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray
"\(in NumPy v2.3\)") `NDArrayNumericExpression`)

Returns:

    

`NDArrayNumericExpression` or
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

__mul__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__mul__)

    

Positionally multiply by a ndarray or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to multiply by.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional products.

__ne__(_other_)

    

Returns `True` if the two expressions are not equal.

Examples

    
    
    >>> x = hl.literal(5)
    >>> y = hl.literal(5)
    >>> z = hl.literal(1)
    
    
    
    >>> hl.eval(x != y)
    False
    
    
    
    >>> hl.eval(x != z)
    True
    

Notes

This method will fail with an error if the two expressions are not of
comparable types.

Parameters:

    

**other** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")) – Expression for inequality comparison.

Returns:

    

[`BooleanExpression`](hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression") – `True` if the two expressions are not equal.

__neg__()[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__neg__)

    

Negate elements of the ndarray.

Returns:

    

`NDArrayNumericExpression` – Array expression of the same type.

__sub__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__sub__)

    

Positionally subtract a ndarray or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to subtract.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional differences.

__truediv__(_other_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.__truediv__)

    

Positionally divide by a ndarray or a scalar.

Parameters:

    

**other**
([`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression") or `NDArrayNumericExpression`) – Value or
ndarray to divide by.

Returns:

    

`NDArrayNumericExpression` – NDArray of positional quotients.

collect(__localize =True_)

    

Collect all records of an expression into a local list.

Examples

Collect all the values from C1:

    
    
    >>> table1.C1.collect()
    [2, 2, 10, 11]
    

Warning

Extremely experimental.

Warning

The list of records may be very large.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

describe(_handler= <built-in function print>_)

    

Print information about type, index, and dependencies.

_property _dtype

    

The data type of the expression.

Returns:

    

```HailType```

export(_path_ , _delimiter ='\t'_, _missing ='NA'_, _header =True_)

    

Export a field to a text file.

Examples

    
    
    >>> small_mt.GT.export('output/gt.tsv')
    >>> with open('output/gt.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles 0       1       2       3
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.GT.export('output/gt-no-header.tsv', header=False)
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    
    
    
    >>> small_mt.pop.export('output/pops.tsv')
    >>> with open('output/pops.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    sample_idx      pop
    0       1
    1       2
    2       2
    3       2
    
    
    
    >>> small_mt.ancestral_af.export('output/ancestral_af.tsv')
    >>> with open('output/ancestral_af.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles ancestral_af
    1:1     ["A","C"]       3.8152e-01
    1:2     ["A","C"]       7.0588e-01
    1:3     ["A","C"]       4.9991e-01
    1:4     ["A","C"]       3.9616e-01
    
    
    
    >>> small_mt.bn.export('output/bn.tsv')
    >>> with open('output/bn.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    bn
    {"n_populations":3,"n_samples":4,"n_variants":4,"n_partitions":4,"pop_dist":[1,1,1],"fst":[0.1,0.1,0.1],"mixture":false}
    

Notes

For entry-indexed expressions, if there is one column key field, the result of
calling [`str()`](functions/core.html#hail.expr.functions.str
"hail.expr.functions.str") on that field is used as the column header.
Otherwise, each compound column key is converted to JSON and used as a column
header. For example:

    
    
    >>> small_mt = small_mt.key_cols_by(s=small_mt.sample_idx, family='fam1')
    >>> small_mt.GT.export('output/gt-no-header.tsv')
    >>> with open('output/gt-no-header.tsv', 'r') as f:
    ...     for line in f:
    ...         print(line, end='')
    locus   alleles {"s":0,"family":"fam1"} {"s":1,"family":"fam1"} {"s":2,"family":"fam1"} {"s":3,"family":"fam1"}
    1:1     ["A","C"]       0/1     0/0     0/1     0/0
    1:2     ["A","C"]       1/1     0/1     0/1     0/1
    1:3     ["A","C"]       0/0     0/1     0/0     0/0
    1:4     ["A","C"]       0/1     1/1     0/1     0/1
    

Parameters:

    

  * **path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The path to which to export.

  * **delimiter** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string for delimiting columns.

  * **missing** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – The string to output for missing values.

  * **header** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – When `True` include a header line.

map(_f_)

    

Applies an element-wise operation on an NDArray.

Parameters:

    

**f** (function ( (arg) ->
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression"))) – Function to transform each element of the NDArray.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression"). – NDArray where each element has been
transformed according to f.

map2(_other_ , _f_)

    

Applies an element-wise binary operation on two NDArrays.

Parameters:

    

  * **other** (class:.NDArrayExpression, ```ArrayExpression```, numpy NDarray,) – or nested python list/tuples. Both NDArrays must be the same shape or broadcastable into common shape.

  * **f** (function ((arg1, arg2)-> ```Expression```)) – Function to be applied to each element of both NDArrays.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression"). – Element-wise result of applying f to each
index in NDArrays.

_property _ndim

    

The number of dimensions of this ndarray.

Examples

    
    
    >>> nd.ndim
    2
    

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

reshape(_* shape_)

    

Reshape this ndarray to a new shape.

Parameters:

    

**shape** ([`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64") or) – :obj: tuple of
[`Expression`](hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type [`tint64`](types.html#hail.expr.types.tint64
"hail.expr.types.tint64")

Examples

    
    
    >>> v = hl.nd.array([1, 2, 3, 4]) 
    >>> m = v.reshape((2, 2)) 
    

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression").

_property _shape

    

The shape of this ndarray.

Examples

    
    
    >>> hl.eval(nd.shape)
    (2, 2)
    

Returns:

    

[`TupleExpression`](hail.expr.TupleExpression.html#hail.expr.TupleExpression
"hail.expr.TupleExpression")

show(_n =None_, _width =None_, _truncate =None_, _types =True_, _handler
=None_, _n_rows =None_, _n_cols =None_)

    

Print the first few records of the expression to the console.

If the expression refers to a value on a keyed axis of a table or matrix
table, then the accompanying keys will be shown along with the records.

Examples

    
    
    >>> table1.SEX.show()
    +-------+-----+
    |    ID | SEX |
    +-------+-----+
    | int32 | str |
    +-------+-----+
    |     1 | "M" |
    |     2 | "M" |
    |     3 | "F" |
    |     4 | "F" |
    +-------+-----+
    
    
    
    >>> hl.literal(123).show()
    +--------+
    | <expr> |
    +--------+
    |  int32 |
    +--------+
    |    123 |
    +--------+
    

Notes

The output can be passed piped to another output source using the handler
argument:

    
    
    >>> ht.foo.show(handler=lambda x: logging.info(x))  
    

Parameters:

    

  * **n** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Maximum number of rows to show.

  * **width** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Horizontal width at which to break columns.

  * **truncate** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)"), optional) – Truncate each field to the given number of characters. If `None`, truncate fields to the given width.

  * **types** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – Print an extra header line with the type of each field.

sum(_axis
=None_)[[source]](_modules/hail/expr/expressions/typed_expressions.html#NDArrayNumericExpression.sum)

    

Sum out one or more axes of an ndarray.

Parameters:

    

**axis** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in
Python v3.9\)")
[`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python
v3.9\)")) – The axis or axes to sum out.

Returns:

    

`NDArrayNumericExpression` or
[`NumericExpression`](hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

summarize(_handler =None_)

    

Compute and print summary information about the expression.

Danger

This functionality is _experimental_. It may not be tested as well as other
parts of Hail and the interface is subject to change.

take(_n_ , __localize =True_)

    

Collect the first n records of an expression.

Examples

Take the first three rows:

    
    
    >>> table1.X.take(3)
    [5, 6, 7]
    

Warning

Extremely experimental.

Parameters:

    

**n** (_int_) – Number of records to take.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)")

transpose(_axes =None_)

    

Permute the dimensions of this ndarray according to the ordering of axes. Axis
j in the ith index of axes maps the jth dimension of the ndarray to the ith
dimension of the output ndarray.

Parameters:

    

**axes** ([`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple
"\(in Python v3.9\)") of
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)"), optional) – The new ordering of the ndarray’s dimensions.

Notes

Does nothing on ndarrays of dimensionality 0 or 1.

Returns:

    

[`NDArrayExpression`](hail.expr.NDArrayExpression.html#hail.expr.NDArrayExpression
"hail.expr.NDArrayExpression").

---

# Collection functions

Collection constructors

```dict```(collection) | Creates a dictionary.  
---|---  
```empty_dict```(key_type, value_type) | Returns an empty dictionary with key type key_type and value type value_type.  
```array```(collection) | Construct an array expression.  
```empty_array```(t) | Returns an empty array of elements of a type t.  
```set```(collection) | Convert a set expression.  
```empty_set```(t) | Returns an empty set of elements of a type t.  
  
Collection functions

`len`(x) | Returns the size of a collection or string.  
---|---  
`map`(f, *collections) | Transform each element of a collection.  
`flatmap`(f, collection) | Map each element of the collection to a new collection, and flatten the results.  
`starmap`(f, collection) | Transform each element of a collection of tuples.  
`zip`(*arrays[, fill_missing]) | Zip together arrays into a single array.  
`enumerate`(a[, start, index_first]) | Returns an array of (index, element) tuples.  
`zip_with_index`(a[, index_first]) | Deprecated in favor of `enumerate()`.  
`flatten`(collection) | Flatten a nested collection by concatenating sub-collections.  
`any`(*args) | Check for any `True` in boolean expressions or collections of booleans.  
`all`(*args) | Check for all `True` in boolean expressions or collections of booleans.  
`filter`(f, collection) | Returns a new collection containing elements where f returns `True`.  
`sorted`(collection[, key, reverse]) | Returns a sorted array.  
`find`(f, collection) | Returns the first element where f returns `True`.  
`group_by`(f, collection) | Group collection elements into a dict according to a lambda function.  
`fold`(f, zero, collection) | Reduces a collection with the given function f, provided the initial value zero.  
`array_scan`(f, zero, a) | Map each element of a to cumulative value of function f, with initial value zero.  
`reversed`(x) | Reverses the elements of a collection.  
`keyed_intersection`(*arrays, key) | Compute the intersection of sorted arrays on a given key.  
`keyed_union`(*arrays, key) | Compute the distinct union of sorted arrays on a given key.  
  
hail.expr.functions.len(_x_)[[source]](../_modules/hail/expr/functions.html#len)

    

Returns the size of a collection or string.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.len(a))
    4
    
    
    
    >>> hl.eval(hl.len(s))
    6
    
    
    
    >>> hl.eval(hl.len("12345"))
    5
    

Parameters:

    

**x**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") or
[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")) – String or collection expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.map(_f_ , _*
collections_)[[source]](../_modules/hail/expr/functions.html#map)

    

Transform each element of a collection.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> b = [2, 4, 6, 8]
    
    
    
    >>> hl.eval(hl.map(lambda x: hl.len(x), a))
    [3, 5, 5, 3]
    
    
    
    >>> hl.eval(hl.map(lambda s, n: hl.len(s) + n, a, b))
    [5, 9, 11, 11]
    

Parameters:

    

  * **f** (function ( (*arg) -> ```Expression```)) – Function to transform each element of the collection.

  * ***collections** (```ArrayExpression``` or ```SetExpression```) – A single collection expression or multiple array expressions.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). – Collection where each element has been
transformed by f.

hail.expr.functions.flatmap(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#flatmap)

    

Map each element of the collection to a new collection, and flatten the
results.

Examples

    
    
    >>> a = [[0, 1], [1, 2], [4, 5, 6, 7]]
    
    
    
    >>> hl.eval(hl.flatmap(lambda x: x[1:], a))
    [1, 2, 5, 6, 7]
    

Parameters:

    

  * **f** (function ( (arg) -> ```CollectionExpression```)) – Function from the element type of the collection to the type of the collection. For instance, flatmap on a `set<str>` should take a `str` and return a `set`.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")

hail.expr.functions.starmap(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#starmap)

    

Transform each element of a collection of tuples.

Examples

    
    
    >>> a = [(1, 5), (3, 2), (7, 8)]
    
    
    
    >>> hl.eval(hl.starmap(lambda x, y: hl.if_else(x < y, x, y), a))
    [1, 2, 7]
    

Parameters:

    

  * **f** (function ( (*args) -> ```Expression```)) – Function to transform each element of the collection.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). – Collection where each element has been
transformed by f.

hail.expr.functions.zip(_* arrays_, _fill_missing
=False_)[[source]](../_modules/hail/expr/functions.html#zip)

    

Zip together arrays into a single array.

Examples

    
    
    >>> hl.eval(hl.zip([1, 2, 3], [4, 5, 6]))
    [(1, 4), (2, 5), (3, 6)]
    

If the arrays are different lengths, the behavior is decided by the
fill_missing parameter.

    
    
    >>> hl.eval(hl.zip([1], [10, 20], [100, 200, 300]))
    [(1, 10, 100)]
    
    
    
    >>> hl.eval(hl.zip([1], [10, 20], [100, 200, 300], fill_missing=True))
    [(1, 10, 100), (None, 20, 200), (None, None, 300)]
    

Notes

The element type of the resulting array is a
```ttuple``` with
a field for each array.

Parameters:

    

  * **arrays** (: variable-length args of ```ArrayExpression```) – Array expressions.

  * **fill_missing** (```bool```) – If `False`, return an array with length equal to the shortest length of the arrays. If `True`, return an array equal to the longest length of the arrays, by extending the shorter arrays with missing values.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.enumerate(_a_ , _start =0_, _*_ , _index_first
=True_)[[source]](../_modules/hail/expr/functions.html#enumerate)

    

Returns an array of (index, element) tuples.

Examples

    
    
    >>> hl.eval(hl.enumerate(['A', 'B', 'C']))
    [(0, 'A'), (1, 'B'), (2, 'C')]
    
    
    
    >>> hl.eval(hl.enumerate(['A', 'B', 'C'], start=3))
    [(3, 'A'), (4, 'B'), (5, 'C')]
    
    
    
    >>> hl.eval(hl.enumerate(['A', 'B', 'C'], index_first=False))
    [('A', 0), ('B', 1), ('C', 2)]
    

Parameters:

    

  * **a** (```ArrayExpression```)

  * **start** (```Int32Expression```) – The index value from which the counter is started, 0 by default.

  * **index_first** (```bool```) – If `True`, the index is the first value of the element tuples. If `False`, the index is the second value.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of (index, element) or (element, index)
tuples.

hail.expr.functions.zip_with_index(_a_ , _index_first
=True_)[[source]](../_modules/hail/expr/functions.html#zip_with_index)

    

Deprecated in favor of `enumerate()`.

Returns an array of (index, element) tuples.

Examples

    
    
    >>> hl.eval(hl.zip_with_index(['A', 'B', 'C']))
    [(0, 'A'), (1, 'B'), (2, 'C')]
    
    
    
    >>> hl.eval(hl.zip_with_index(['A', 'B', 'C'], index_first=False))
    [('A', 0), ('B', 1), ('C', 2)]
    

Parameters:

    

  * **a** (```ArrayExpression```)

  * **index_first** (```bool```) – If `True`, the index is the first value of the element tuples. If `False`, the index is the second value.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Array of (index, element) or (element, index)
tuples.

hail.expr.functions.flatten(_collection_)[[source]](../_modules/hail/expr/functions.html#flatten)

    

Flatten a nested collection by concatenating sub-collections.

Examples

    
    
    >>> a = [[1, 2], [2, 3]]
    
    
    
    >>> hl.eval(hl.flatten(a))
    [1, 2, 2, 3]
    

Parameters:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")) – Collection with element type
```tarray``` or
```tset```.

Returns:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"))

hail.expr.functions.any(_*
args_)[[source]](../_modules/hail/expr/functions.html#any)

    

Check for any `True` in boolean expressions or collections of booleans.

`any()` comes in three forms:

  1. `hl.any(boolean, ...)`. Is at least one argument `True`?

  2. `hl.any(collection)`. Is at least one element of this collection `True`?

  3. `hl.any(function, collection)`. Does `function` return `True` for at least one value in this collection?

Examples

The first form:

    
    
    >>> hl.eval(hl.any())
    False
    
    
    
    >>> hl.eval(hl.any(True))
    True
    
    
    
    >>> hl.eval(hl.any(False))
    False
    
    
    
    >>> hl.eval(hl.any(False, False, True, False))
    True
    

The second form:

    
    
    >>> hl.eval(hl.any([False, True, False]))
    True
    
    
    
    >>> hl.eval(hl.any([False, False, False]))
    False
    

The third form:

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.any(lambda x: x[-1] == 'x', a))
    True
    
    
    
    >>> hl.eval(hl.any(lambda x: x % 4 == 0, s))
    False
    

Notes

`any()` returns `False` when given an empty array or empty argument list.

hail.expr.functions.all(_*
args_)[[source]](../_modules/hail/expr/functions.html#all)

    

Check for all `True` in boolean expressions or collections of booleans.

`all()` comes in three forms:

  1. `hl.all(boolean, ...)`. Are all arguments `True`?

  2. `hl.all(collection)`. Are all elements of the collection `True`?

  3. `hl.all(function, collection)`. Does `function` return `True` for all values in this collection?

Examples

The first form:

    
    
    >>> hl.eval(hl.all())
    True
    
    
    
    >>> hl.eval(hl.all(True))
    True
    
    
    
    >>> hl.eval(hl.all(False))
    False
    
    
    
    >>> hl.eval(hl.all(True, True, True))
    True
    
    
    
    >>> hl.eval(hl.all(False, False, True, False))
    False
    

The second form:

    
    
    >>> hl.eval(hl.all([False, True, False]))
    False
    
    
    
    >>> hl.eval(hl.all([True, True, True]))
    True
    

The third form:

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.all(lambda x: hl.len(x) > 3, a))
    False
    
    
    
    >>> hl.eval(hl.all(lambda x: x < 10, s))
    True
    

Notes

`all()` returns `True` when given an empty array or empty argument list.

hail.expr.functions.filter(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#filter)

    

Returns a new collection containing elements where f returns `True`.

Examples

    
    
    >>> a = [1, 2, 3, 4]
    >>> s = {'Alice', 'Bob', 'Charlie'}
    
    
    
    >>> hl.eval(hl.filter(lambda x: x % 2 == 0, a))
    [2, 4]
    
    
    
    >>> hl.eval(hl.filter(lambda x: ~(x[-1] == 'e'), s))
    {'Bob'}
    

Notes

Returns a same-type expression; evaluated on a
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"), returns a
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression"). Evaluated on an
[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression"), returns an
[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

Parameters:

    

  * **f** (function ( (arg) -> ```BooleanExpression```)) – Function to evaluate for each element of the collection. Must return a ```BooleanExpression```.

  * **collection** (```ArrayExpression``` or ```SetExpression```.) – Array or set expression to filter.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression") – Expression of the same type as collection.

hail.expr.functions.sorted(_collection_ , _key =None_, _reverse
=False_)[[source]](../_modules/hail/expr/functions.html#sorted)

    

Returns a sorted array.

Examples

    
    
    >>> a = ['Charlie', 'Alice', 'Bob']
    
    
    
    >>> hl.eval(hl.sorted(a))
    ['Alice', 'Bob', 'Charlie']
    
    
    
    >>> hl.eval(hl.sorted(a, reverse=True))
    ['Charlie', 'Bob', 'Alice']
    
    
    
    >>> hl.eval(hl.sorted(a, key=lambda x: hl.len(x)))
    ['Bob', 'Alice', 'Charlie']
    

Notes

The ordered types are [`tstr`](../types.html#hail.expr.types.tstr
"hail.expr.types.tstr") and numeric types.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression``` or ```DictExpression```) – Collection to sort.

  * **key** (function ( (arg) -> ```Expression```), optional) – Function to evaluate for each element to compute sort key.

  * **reverse** (```BooleanExpression```) – Sort in descending order.

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") – Sorted array.

hail.expr.functions.find(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#find)

    

Returns the first element where f returns `True`.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> s = {1, 3, 5, 6, 7, 9}
    
    
    
    >>> hl.eval(hl.find(lambda x: x[-1] == 'x', a))
    'fox'
    
    
    
    >>> hl.eval(hl.find(lambda x: x % 4 == 0, s))
    None
    

Notes

If f returns `False` for every element, then the result is missing.

Sets are unordered. If collection is of type
```tset```, then the
element returned comes from no guaranteed ordering.

Parameters:

    

  * **f** (function ( (arg) -> ```BooleanExpression```)) – Function to evaluate for each element of the collection. Must return a ```BooleanExpression```.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – Expression whose type is the element type of the
collection.

hail.expr.functions.group_by(_f_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#group_by)

    

Group collection elements into a dict according to a lambda function.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> hl.eval(hl.group_by(lambda x: hl.len(x), a))
    {3: ['The', 'fox'], 5: ['quick', 'brown']}
    

Parameters:

    

  * **f** (function ( (arg) -> ```Expression```)) – Function to evaluate for each element of the collection to produce a key for the resulting dictionary.

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression.

Returns:

    

[`DictExpression`](../hail.expr.DictExpression.html#hail.expr.DictExpression
"hail.expr.DictExpression"). – Dictionary keyed by results of f.

hail.expr.functions.fold(_f_ , _zero_ ,
_collection_)[[source]](../_modules/hail/expr/functions.html#fold)

    

Reduces a collection with the given function f, provided the initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.fold(lambda i, j: i + j, 0, a))
    3
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

  * **collection** (```ArrayExpression``` or ```SetExpression```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.array_scan(_f_ , _zero_ ,
_a_)[[source]](../_modules/hail/expr/functions.html#array_scan)

    

Map each element of a to cumulative value of function f, with initial value
zero.

Examples

    
    
    >>> a = [0, 1, 2]
    
    
    
    >>> hl.eval(hl.array_scan(lambda i, j: i + j, 0, a))
    [0, 0, 1, 3]
    

Parameters:

    

  * **f** (function ( (```Expression```, ```Expression```) -> ```Expression```)) – Function which takes the cumulative value and the next element, and returns a new value.

  * **zero** (```Expression```) – Initial value to pass in as left argument of f.

  * **a** (```ArrayExpression```)

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression").

hail.expr.functions.reversed(_x_)[[source]](../_modules/hail/expr/functions.html#reversed)

    

Reverses the elements of a collection.

Examples

    
    
    >>> a = ['The', 'quick', 'brown', 'fox']
    >>> hl.eval(hl.reversed(a))
    ['fox', 'brown', 'quick', 'The']
    

Parameters:

    

**x**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")) – Array or string expression.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression")

hail.expr.functions.keyed_intersection(_* arrays_,
_key_)[[source]](../_modules/hail/expr/functions.html#keyed_intersection)

    

Compute the intersection of sorted arrays on a given key.

Requires sorted arrays with distinct keys.

Warning

Experimental. Does not support downstream randomness.

Parameters:

    

  * **arrays**

  * **key**

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

hail.expr.functions.keyed_union(_* arrays_,
_key_)[[source]](../_modules/hail/expr/functions.html#keyed_union)

    

Compute the distinct union of sorted arrays on a given key.

Requires sorted arrays with distinct keys.

Warning

Experimental. Does not support downstream randomness.

Parameters:

    

  * **exprs**

  * **key**

Returns:

    

[`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression")

---

# Call

_class
_hail.genetics.Call[[source]](../_modules/hail/genetics/call.html#Call)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

An object that represents an individual’s call at a genomic locus.

Parameters:

    

  * **alleles** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – List of alleles that compose the call.

  * **phased** ([`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)")) – If `True`, the alleles are phased and the order is specified by alleles.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.GT.take(5`)`. This is rare; it is much more common to
manipulate the
[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression") object, which is constructed using the following
functions:

>   * [`call()`](../functions/genetics.html#hail.expr.functions.call
> "hail.expr.functions.call")
>
>   *
> [`unphased_diploid_gt_index_call()`](../functions/genetics.html#hail.expr.functions.unphased_diploid_gt_index_call
> "hail.expr.functions.unphased_diploid_gt_index_call")
>
>   *
> [`parse_call()`](../functions/genetics.html#hail.expr.functions.parse_call
> "hail.expr.functions.parse_call")
>
>

Attributes

`alleles` | Get the alleles of this call.  
---|---  
`phased` | True if the call is phased.  
`ploidy` | The number of alleles for this call.  
  
Methods

`is_diploid` | True if the ploidy == 2.  
---|---  
`is_haploid` | True if the ploidy == 1.  
`is_het` | True if the call contains two different alleles.  
`is_het_non_ref` | True if the call contains two different alternate alleles.  
`is_het_ref` | True if the call contains one reference and one alternate allele.  
`is_hom_ref` | True if the call has no alternate alleles.  
`is_hom_var` | True if the call contains identical alternate alleles.  
`is_non_ref` | True if the call contains any non-reference alleles.  
`n_alt_alleles` | Returns the count of non-reference alleles.  
`one_hot_alleles` | Returns a list containing the one-hot encoded representation of the called alleles.  
`unphased_diploid_gt_index` | Return the genotype index for unphased, diploid calls.  
  
_property _alleles

    

Get the alleles of this call.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`int`](https://docs.python.org/3.9/library/functions.html#int
"\(in Python v3.9\)")

is_diploid()[[source]](../_modules/hail/genetics/call.html#Call.is_diploid)

    

True if the ploidy == 2.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_haploid()[[source]](../_modules/hail/genetics/call.html#Call.is_haploid)

    

True if the ploidy == 1.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_het()[[source]](../_modules/hail/genetics/call.html#Call.is_het)

    

True if the call contains two different alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_het_non_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_het_non_ref)

    

True if the call contains two different alternate alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_het_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_het_ref)

    

True if the call contains one reference and one alternate allele.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_hom_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_hom_ref)

    

True if the call has no alternate alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_hom_var()[[source]](../_modules/hail/genetics/call.html#Call.is_hom_var)

    

True if the call contains identical alternate alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

is_non_ref()[[source]](../_modules/hail/genetics/call.html#Call.is_non_ref)

    

True if the call contains any non-reference alleles.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

n_alt_alleles()[[source]](../_modules/hail/genetics/call.html#Call.n_alt_alleles)

    

Returns the count of non-reference alleles.

Return type:

    

[int](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

one_hot_alleles(_n_alleles_)[[source]](../_modules/hail/genetics/call.html#Call.one_hot_alleles)

    

Returns a list containing the one-hot encoded representation of the called
alleles.

Examples

    
    
    >>> n_alleles = 2
    >>> hom_ref = hl.Call([0, 0])
    >>> het = hl.Call([0, 1])
    >>> hom_var = hl.Call([1, 1])
    
    
    
    >>> het.one_hot_alleles(n_alleles)
    [1, 1]
    
    
    
    >>> hom_var.one_hot_alleles(n_alleles)
    [0, 2]
    

Notes

This one-hot representation is the positional sum of the one-hot encoding for
each called allele. For a biallelic variant, the one-hot encoding for a
reference allele is [1, 0] and the one-hot encoding for an alternate allele is
[0, 1].

Parameters:

    

**n_alleles** ([`int`](https://docs.python.org/3.9/library/functions.html#int
"\(in Python v3.9\)")) – Number of total alleles, including the reference.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`int`](https://docs.python.org/3.9/library/functions.html#int
"\(in Python v3.9\)")

_property _phased

    

True if the call is phased.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

_property _ploidy

    

The number of alleles for this call.

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

unphased_diploid_gt_index()[[source]](../_modules/hail/genetics/call.html#Call.unphased_diploid_gt_index)

    

Return the genotype index for unphased, diploid calls.

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

---

# Genetics functions

`locus`(contig, pos[, reference_genome]) | Construct a locus expression from a chromosome and position.  
---|---  
`locus_from_global_position`(global_pos[, ...]) | Constructs a locus expression from a global position and a reference genome.  
`locus_interval`(contig, start, end[, ...]) | Construct a locus interval expression.  
`parse_locus`(s[, reference_genome]) | Construct a locus expression by parsing a string or string expression.  
`parse_variant`(s[, reference_genome]) | Construct a struct with a locus and alleles by parsing a string.  
`parse_locus_interval`(s[, reference_genome, ...]) | Construct a locus interval expression by parsing a string or string expression.  
`variant_str`(*args) | Create a variant colon-delimited string.  
`call`(*alleles[, phased]) | Construct a call expression.  
`unphased_diploid_gt_index_call`(gt_index) | Construct an unphased, diploid call from a genotype index.  
`parse_call`(s) | Construct a call expression by parsing a string or string expression.  
`downcode`(c, i) | Create a new call by setting all alleles other than i to ref  
`triangle`(n) | Returns the triangle number of n.  
`is_snp`(ref, alt) | Returns `True` if the alleles constitute a single nucleotide polymorphism.  
`is_mnp`(ref, alt) | Returns `True` if the alleles constitute a multiple nucleotide polymorphism.  
`is_transition`(ref, alt) | Returns `True` if the alleles constitute a transition.  
`is_transversion`(ref, alt) | Returns `True` if the alleles constitute a transversion.  
`is_insertion`(ref, alt) | Returns `True` if the alleles constitute an insertion.  
`is_deletion`(ref, alt) | Returns `True` if the alleles constitute a deletion.  
`is_indel`(ref, alt) | Returns `True` if the alleles constitute an insertion or deletion.  
`is_star`(ref, alt) | Returns `True` if the alleles constitute an upstream deletion.  
`is_complex`(ref, alt) | Returns `True` if the alleles constitute a complex polymorphism.  
`is_strand_ambiguous`(ref, alt) | Returns `True` if the alleles are strand ambiguous.  
`is_valid_contig`(contig[, reference_genome]) | Returns `True` if contig is a valid contig name in reference_genome.  
`is_valid_locus`(contig, position[, ...]) | Returns `True` if contig and position is a valid site in reference_genome.  
`contig_length`(contig[, reference_genome]) | Returns the length of contig in reference_genome.  
`allele_type`(ref, alt) | Returns the type of the polymorphism as a string.  
`numeric_allele_type`(ref, alt) | Returns the type of the polymorphism as an integer.  
`pl_dosage`(pl) | Return expected genotype dosage from array of Phred-scaled genotype likelihoods with uniform prior.  
`gp_dosage`(gp) | Return expected genotype dosage from array of genotype probabilities.  
`get_sequence`(contig, position[, before, ...]) | Return the reference sequence at a given locus.  
`mendel_error_code`(locus, is_female, father, ...) | Compute a Mendelian violation code for genotypes.  
`liftover`(x, dest_reference_genome[, ...]) | Lift over coordinates to a different reference genome.  
`min_rep`(locus, alleles) | Computes the minimal representation of a (locus, alleles) polymorphism.  
`reverse_complement`(s[, rna]) | Reverses the string and translates base pairs into their complements .  
  
hail.expr.functions.locus(_contig_ , _pos_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#locus)

    

Construct a locus expression from a chromosome and position.

Examples

    
    
    >>> hl.eval(hl.locus("1", 10000, reference_genome='GRCh37'))
    Locus(contig=1, position=10000, reference_genome=GRCh37)
    

Parameters:

    

  * **contig** (str or ```StringExpression```) – Chromosome.

  * **pos** (int or ```Expression``` of type ```tint32```) – Base position along the chromosome.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use.

Returns:

    

[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")

hail.expr.functions.locus_from_global_position(_global_pos_ ,
_reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#locus_from_global_position)

    

Constructs a locus expression from a global position and a reference genome.
The inverse of
[`LocusExpression.global_position()`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression.global_position
"hail.expr.LocusExpression.global_position").

Examples

    
    
    >>> hl.eval(hl.locus_from_global_position(0))
    Locus(contig=1, position=1, reference_genome=GRCh37)
    
    
    
    >>> hl.eval(hl.locus_from_global_position(2824183054))
    Locus(contig=21, position=42584230, reference_genome=GRCh37)
    
    
    
    >>> hl.eval(hl.locus_from_global_position(2824183054, reference_genome='GRCh38'))
    Locus(contig=chr22, position=1, reference_genome=GRCh38)
    

Parameters:

    

  * **global_pos** (int or ```Expression``` of type ```tint64```) – Global base position along the reference genome.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use for converting the global position to a contig and local position.

Returns:

    

[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")

hail.expr.functions.locus_interval(_contig_ , _start_ , _end_ ,
_includes_start =True_, _includes_end =False_, _reference_genome ='default'_,
_invalid_missing
=False_)[[source]](../_modules/hail/expr/functions.html#locus_interval)

    

Construct a locus interval expression.

Examples

    
    
    >>> hl.eval(hl.locus_interval("1", 100, 1000, reference_genome='GRCh37'))
    Interval(start=Locus(contig=1, position=100, reference_genome=GRCh37),
             end=Locus(contig=1, position=1000, reference_genome=GRCh37),
             includes_start=True,
             includes_end=False)
    

Parameters:

    

  * **contig** (```StringExpression```) – Contig name.

  * **start** (```Int32Expression```) – Starting base position.

  * **end** (```Int32Expression```) – End base position.

  * **includes_start** (```BooleanExpression```) – If `True`, interval includes start point.

  * **includes_end** (```BooleanExpression```) – If `True`, interval includes end point.

  * **reference_genome** (```str``` or ```hail.genetics.ReferenceGenome```) – Reference genome to use.

  * **invalid_missing** (```BooleanExpression```) – If `True`, invalid intervals are set to NA rather than causing an exception.

Returns:

    

[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

hail.expr.functions.parse_locus(_s_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#parse_locus)

    

Construct a locus expression by parsing a string or string expression.

Examples

    
    
    >>> hl.eval(hl.parse_locus('1:10000', reference_genome='GRCh37'))
    Locus(contig=1, position=10000, reference_genome=GRCh37)
    

Notes

This method expects strings of the form `contig:position`, e.g. `16:29500000`
or `X:123456`.

Parameters:

    

  * **s** (str or ```StringExpression```) – String to parse.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use.

Returns:

    

[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")

hail.expr.functions.parse_variant(_s_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#parse_variant)

    

Construct a struct with a locus and alleles by parsing a string.

Examples

    
    
    >>> hl.eval(hl.parse_variant('1:100000:A:T,C', reference_genome='GRCh37'))
    Struct(locus=Locus(contig=1, position=100000, reference_genome=GRCh37), alleles=['A', 'T', 'C'])
    

Notes

This method returns an expression of type
```tstruct```
with the following fields:

>   * locus ([`tlocus`](../types.html#hail.expr.types.tlocus
> "hail.expr.types.tlocus"))
>
>   * alleles ([`tarray`](../types.html#hail.expr.types.tarray
> "hail.expr.types.tarray") of [`tstr`](../types.html#hail.expr.types.tstr
> "hail.expr.types.tstr"))
>
>

Parameters:

    

  * **s** (```StringExpression```) – String to parse.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – Struct with fields locus and alleles.

hail.expr.functions.parse_locus_interval(_s_ , _reference_genome ='default'_,
_invalid_missing
=False_)[[source]](../_modules/hail/expr/functions.html#parse_locus_interval)

    

Construct a locus interval expression by parsing a string or string
expression.

Examples

    
    
    >>> hl.eval(hl.parse_locus_interval('1:1000-2000', reference_genome='GRCh37'))
    Interval(start=Locus(contig=1, position=1000, reference_genome=GRCh37),
             end=Locus(contig=1, position=2000, reference_genome=GRCh37),
             includes_start=True,
             includes_end=False)
    
    
    
    >>> hl.eval(hl.parse_locus_interval('1:start-10M', reference_genome='GRCh37'))
    Interval(start=Locus(contig=1, position=1, reference_genome=GRCh37),
             end=Locus(contig=1, position=10000000, reference_genome=GRCh37),
             includes_start=True,
             includes_end=False)
    

Notes

The start locus must precede the end locus. The default bounds of the interval
are left-inclusive and right-exclusive. To change this, add one of `[` or `(`
at the beginning of the string for left-inclusive or left-exclusive
respectively. Likewise, add one of `]` or `)` at the end of the string for
right-inclusive or right-exclusive respectively.

There are several acceptable representations for s.

`CHR1:POS1-CHR2:POS2` is the fully specified representation, and we use this
to define the various shortcut representations.

In a `POS` field, `start` (`Start`, `START`) stands for 1.

In a `POS` field, `end` (`End`, `END`) stands for the contig length.

In a `POS` field, the qualifiers `m` (`M`) and `k` (`K`) multiply the given
number by `1,000,000` and `1,000`, respectively. `1.6K` is short for 1600, and
`29M` is short for 29000000.

`CHR:POS1-POS2` stands for `CHR:POS1-CHR:POS2`

`CHR1-CHR2` stands for `CHR1:START-CHR2:END`

`CHR` stands for `CHR:START-CHR:END`

Note

The bounds of the interval must be valid loci for the reference genome (contig
in reference genome and position is within the range [1-END]) except in the
case where the position is `0` **AND** the interval is **left-exclusive**
which is normalized to be `1` and left-inclusive. Likewise, in the case where
the position is `END + 1` **AND** the interval is **right-exclusive** which is
normalized to be `END` and right-inclusive.

Parameters:

    

  * **s** (str or ```StringExpression```) – String to parse.

  * **reference_genome** (```str``` or ```hail.genetics.ReferenceGenome```) – Reference genome to use.

  * **invalid_missing** (```BooleanExpression```) – If `True`, invalid intervals are set to NA rather than causing an exception.

Returns:

    

[`IntervalExpression`](../hail.expr.IntervalExpression.html#hail.expr.IntervalExpression
"hail.expr.IntervalExpression")

hail.expr.functions.variant_str(_*
args_)[[source]](../_modules/hail/expr/functions.html#variant_str)

    

Create a variant colon-delimited string.

Parameters:

    

**args** – Arguments (see notes).

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

Notes

Expects either one argument of type `struct{locus: locus<RG>, alleles:
array<str>`, or two arguments of type `locus<RG>` and `array<str>`. The
function returns a string of the form

    
    
    CHR:POS:REF:ALT1,ALT2,...ALTN
    e.g.
    1:1:A:T
    16:250125:AAA:A,CAA
    

Examples

    
    
    >>> hl.eval(hl.variant_str(hl.locus('1', 10000), ['A', 'T', 'C']))
    '1:10000:A:T,C'
    

hail.expr.functions.call(_* alleles_, _phased
=False_)[[source]](../_modules/hail/expr/functions.html#call)

    

Construct a call expression.

Examples

    
    
    >>> hl.eval(hl.call(1, 0))
    Call(alleles=[0, 1], phased=False)
    

Parameters:

    

  * **alleles** (variable-length args of ```int``` or ```Expression``` of type ```tint32```) – List of allele indices.

  * **phased** (```bool```) – If `True`, preserve the order of alleles.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.unphased_diploid_gt_index_call(_gt_index_)[[source]](../_modules/hail/expr/functions.html#unphased_diploid_gt_index_call)

    

Construct an unphased, diploid call from a genotype index.

Examples

    
    
    >>> hl.eval(hl.unphased_diploid_gt_index_call(4))
    Call(alleles=[1, 2], phased=False)
    

Parameters:

    

**gt_index** ([`int`](constructors.html#hail.expr.functions.int
"hail.expr.functions.int") or
[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```) –
Unphased, diploid genotype index.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.parse_call(_s_)[[source]](../_modules/hail/expr/functions.html#parse_call)

    

Construct a call expression by parsing a string or string expression.

Examples

    
    
    >>> hl.eval(hl.parse_call('0|2'))
    Call(alleles=[0, 2], phased=True)
    

Notes

This method expects strings in the following format:

ploidy | Phased | Unphased  
---|---|---  
0 | `|-` | `-`  
1 | `|i` | `i`  
2 | `i|j` | `i/j`  
3 | `i|j|k` | `i/j/k`  
N | `i|j|k|...|N` | `i/j/k/.../N`  
  
Parameters:

    

**s** (str or
[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")) – String to parse.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.downcode(_c_ ,
_i_)[[source]](../_modules/hail/expr/functions.html#downcode)

    

Create a new call by setting all alleles other than i to ref

Examples

Preserve the third allele and downcode all other alleles to reference.

    
    
    >>> hl.eval(hl.downcode(hl.call(1, 2), 2))
    Call(alleles=[0, 1], phased=False)
    
    
    
    >>> hl.eval(hl.downcode(hl.call(2, 2), 2))
    Call(alleles=[1, 1], phased=False)
    
    
    
    >>> hl.eval(hl.downcode(hl.call(0, 1), 2))
    Call(alleles=[0, 0], phased=False)
    

Parameters:

    

  * **c** (```CallExpression```) – A call.

  * **i** (```Expression``` of type ```tint32```) – The index of the allele that will be sent to the alternate allele. All other alleles will be downcoded to reference.

Returns:

    

[`CallExpression`](../hail.expr.CallExpression.html#hail.expr.CallExpression
"hail.expr.CallExpression")

hail.expr.functions.triangle(_n_)[[source]](../_modules/hail/expr/functions.html#triangle)

    

Returns the triangle number of n.

Examples

    
    
    >>> hl.eval(hl.triangle(3))
    6
    

Notes

The calculation is `n * (n + 1) / 2`.

Parameters:

    

**n** ([`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.is_snp(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_snp)

    

Returns `True` if the alleles constitute a single nucleotide polymorphism.

Examples

    
    
    >>> hl.eval(hl.is_snp('A', 'T'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_mnp(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_mnp)

    

Returns `True` if the alleles constitute a multiple nucleotide polymorphism.

Examples

    
    
    >>> hl.eval(hl.is_mnp('AA', 'GT'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_transition(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_transition)

    

Returns `True` if the alleles constitute a transition.

Examples

    
    
    >>> hl.eval(hl.is_transition('A', 'T'))
    False
    
    
    
    >>> hl.eval(hl.is_transition('AAA', 'AGA'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_transversion(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_transversion)

    

Returns `True` if the alleles constitute a transversion.

Examples

    
    
    >>> hl.eval(hl.is_transversion('A', 'T'))
    True
    
    
    
    >>> hl.eval(hl.is_transversion('AAA', 'AGA'))
    False
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_insertion(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_insertion)

    

Returns `True` if the alleles constitute an insertion.

Examples

    
    
    >>> hl.eval(hl.is_insertion('A', 'ATT'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_deletion(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_deletion)

    

Returns `True` if the alleles constitute a deletion.

Examples

    
    
    >>> hl.eval(hl.is_deletion('ATT', 'A'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_indel(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_indel)

    

Returns `True` if the alleles constitute an insertion or deletion.

Examples

    
    
    >>> hl.eval(hl.is_indel('ATT', 'A'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_star(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_star)

    

Returns `True` if the alleles constitute an upstream deletion.

Examples

    
    
    >>> hl.eval(hl.is_star('A', '*'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_complex(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_complex)

    

Returns `True` if the alleles constitute a complex polymorphism.

Examples

    
    
    >>> hl.eval(hl.is_complex('ATT', 'GCAC'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_strand_ambiguous(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#is_strand_ambiguous)

    

Returns `True` if the alleles are strand ambiguous.

Strand ambiguous allele pairs are `A/T`, `T/A`, `C/G`, and `G/C` where the
first allele is ref and the second allele is alt.

Examples

    
    
    >>> hl.eval(hl.is_strand_ambiguous('A', 'T'))
    True
    

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_valid_contig(_contig_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#is_valid_contig)

    

Returns `True` if contig is a valid contig name in reference_genome.

Examples

    
    
    >>> hl.eval(hl.is_valid_contig('1', reference_genome='GRCh37'))
    True
    
    
    
    >>> hl.eval(hl.is_valid_contig('chr1', reference_genome='GRCh37'))
    False
    

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```)

  * **reference_genome** (```str``` or ```ReferenceGenome```)

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.is_valid_locus(_contig_ , _position_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#is_valid_locus)

    

Returns `True` if contig and position is a valid site in reference_genome.

Examples

    
    
    >>> hl.eval(hl.is_valid_locus('1', 324254, 'GRCh37'))
    True
    
    
    
    >>> hl.eval(hl.is_valid_locus('chr1', 324254, 'GRCh37'))
    False
    

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```)

  * **position** (```Expression``` of type ```tint```)

  * **reference_genome** (```str``` or ```ReferenceGenome```)

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.contig_length(_contig_ , _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#contig_length)

    

Returns the length of contig in reference_genome.

Examples

    
    
    >>> hl.eval(hl.contig_length('5', reference_genome='GRCh37'))
    180915260
    

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```)

  * **reference_genome** (```str``` or ```ReferenceGenome```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

hail.expr.functions.allele_type(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#allele_type)

    

Returns the type of the polymorphism as a string.

Examples

    
    
    >>> hl.eval(hl.allele_type('A', 'T'))
    'SNP'
    
    
    
    >>> hl.eval(hl.allele_type('ATT', 'A'))
    'Deletion'
    

Notes

The possible return values are:

    

  * `"SNP"`

  * `"MNP"`

  * `"Insertion"`

  * `"Deletion"`

  * `"Complex"`

  * `"Star"`

  * `"Symbolic"`

  * `"Unknown"`

Parameters:

    

  * **ref** (```StringExpression```) – Reference allele.

  * **alt** (```StringExpression```) – Alternate allele.

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

hail.expr.functions.numeric_allele_type(_ref_ ,
_alt_)[[source]](../_modules/hail/expr/functions.html#numeric_allele_type)

    

Returns the type of the polymorphism as an integer. The value returned is the
integer value of
[`AlleleType`](../genetics/hail.genetics.AlleleType.html#hail.genetics.AlleleType
"hail.genetics.AlleleType") representing that kind of polymorphism.

Examples

    
    
    >>> hl.eval(hl.numeric_allele_type('A', 'T')) == AlleleType.SNP
    True
    

Notes

The values of
[`AlleleType`](../genetics/hail.genetics.AlleleType.html#hail.genetics.AlleleType
"hail.genetics.AlleleType") are not stable and thus should not be relied upon
across hail versions.

hail.expr.functions.pl_dosage(_pl_)[[source]](../_modules/hail/expr/functions.html#pl_dosage)

    

Return expected genotype dosage from array of Phred-scaled genotype
likelihoods with uniform prior. Only defined for bi-allelic variants. The pl
argument must be length 3.

For a PL array `[a, b, c]`, let:

\\[a^\prime = 10^{-a/10} \\\ b^\prime = 10^{-b/10} \\\ c^\prime = 10^{-c/10}
\\\\\\]

The genotype dosage is given by:

\\[\frac{b^\prime + 2 c^\prime} {a^\prime + b^\prime +c ^\prime}\\]

Examples

    
    
    >>> hl.eval(hl.pl_dosage([5, 10, 100]))
    0.24025307377482674
    

Parameters:

    

**pl**
([`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") of type
```tint32```) –
Length 3 array of bi-allelic Phred-scaled genotype likelihoods

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.gp_dosage(_gp_)[[source]](../_modules/hail/expr/functions.html#gp_dosage)

    

Return expected genotype dosage from array of genotype probabilities.

Examples

    
    
    >>> hl.eval(hl.gp_dosage([0.0, 0.5, 0.5]))
    1.5
    

Notes

This function is only defined for bi-allelic variants. The gp argument must be
length 3. The value is `gp[1] + 2 * gp[2]`.

Parameters:

    

**gp** ([`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tarray``` of
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) – Length 3 array of bi-allelic genotype
probabilities

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.get_sequence(_contig_ , _position_ , _before =0_, _after
=0_, _reference_genome
='default'_)[[source]](../_modules/hail/expr/functions.html#get_sequence)

    

Return the reference sequence at a given locus.

Examples

Return the reference allele for `'GRCh37'` at the locus `'1:45323'`:

    
    
    >>> hl.eval(hl.get_sequence('1', 45323, reference_genome='GRCh37')) 
    "T"
    

Notes

This function requires reference genome has an attached reference sequence.
Use
[`ReferenceGenome.add_sequence()`](../genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome.add_sequence
"hail.genetics.ReferenceGenome.add_sequence") to load and attach a reference
sequence to a reference genome.

Returns `None` if contig and position are not valid coordinates in
reference_genome.

Parameters:

    

  * **contig** (```Expression``` of type ```tstr```) – Locus contig.

  * **position** (```Expression``` of type ```tint32```) – Locus position.

  * **before** (```Expression``` of type ```tint32```, optional) – Number of bases to include before the locus of interest. Truncates at contig boundary.

  * **after** (```Expression``` of type ```tint32```, optional) – Number of bases to include after the locus of interest. Truncates at contig boundary.

  * **reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to use. Must have a reference sequence available.

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

hail.expr.functions.mendel_error_code(_locus_ , _is_female_ , _father_ ,
_mother_ ,
_child_)[[source]](../_modules/hail/expr/functions.html#mendel_error_code)

    

Compute a Mendelian violation code for genotypes.

    
    
    >>> father = hl.call(0, 0)
    >>> mother = hl.call(1, 1)
    >>> child1 = hl.call(0, 1)  # consistent
    >>> child2 = hl.call(0, 0)  # Mendel error
    >>> locus = hl.locus('2', 2000000)
    
    
    
    >>> hl.eval(hl.mendel_error_code(locus, True, father, mother, child1))
    None
    
    
    
    >>> hl.eval(hl.mendel_error_code(locus, True, father, mother, child2))
    7
    

Note

Ignores call phasing, and assumes diploid and biallelic. Haploid calls for
hemiploid samples on sex chromosomes also are acceptable input.

Notes

In the table below, the copy state of a locus with respect to a trio is
defined as follows, where PAR is the [pseudoautosomal
region](https://en.wikipedia.org/wiki/Pseudoautosomal_region) (PAR) of X and Y
defined by the reference genome and the autosome is defined by
[`LocusExpression.in_autosome()`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression.in_autosome
"hail.expr.LocusExpression.in_autosome"):

  * Auto – in autosome or in PAR, or in non-PAR of X and female child

  * HemiX – in non-PAR of X and male child

  * HemiY – in non-PAR of Y and male child

Any refers to the set { HomRef, Het, HomVar, NoCall } and ~ denotes complement
in this set.

Code | Dad | Mom | Kid | Copy State | Implicated  
---|---|---|---|---|---  
1 | HomVar | HomVar | Het | Auto | Dad, Mom, Kid  
2 | HomRef | HomRef | Het | Auto | Dad, Mom, Kid  
3 | HomRef | ~HomRef | HomVar | Auto | Dad, Kid  
4 | ~HomRef | HomRef | HomVar | Auto | Mom, Kid  
5 | HomRef | HomRef | HomVar | Auto | Kid  
6 | HomVar | ~HomVar | HomRef | Auto | Dad, Kid  
7 | ~HomVar | HomVar | HomRef | Auto | Mom, Kid  
8 | HomVar | HomVar | HomRef | Auto | Kid  
9 | Any | HomVar | HomRef | HemiX | Mom, Kid  
10 | Any | HomRef | HomVar | HemiX | Mom, Kid  
11 | HomVar | Any | HomRef | HemiY | Dad, Kid  
12 | HomRef | Any | HomVar | HemiY | Dad, Kid  
  
Parameters:

    

  * **locus** (```LocusExpression```)

  * **is_female** (```BooleanExpression```)

  * **father** (```CallExpression```)

  * **mother** (```CallExpression```)

  * **child** (```CallExpression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

hail.expr.functions.liftover(_x_ , _dest_reference_genome_ , _min_match
=0.95_, _include_strand
=False_)[[source]](../_modules/hail/expr/functions.html#liftover)

    

Lift over coordinates to a different reference genome.

Examples

Lift over the locus coordinates from reference genome `'GRCh37'` to
`'GRCh38'`:

    
    
    >>> hl.eval(hl.liftover(hl.locus('1', 1034245, 'GRCh37'), 'GRCh38')) 
    Locus(contig='chr1', position=1098865, reference_genome='GRCh38')
    

Lift over the locus interval coordinates from reference genome `'GRCh37'` to
`'GRCh38'`:

    
    
    >>> hl.eval(hl.liftover(hl.locus_interval('20', 60001, 82456, True, True, 'GRCh37'), 'GRCh38')) 
    Interval(Locus(contig='chr20', position=79360, reference_genome='GRCh38'),
             Locus(contig='chr20', position=101815, reference_genome='GRCh38'),
             True,
             True)
    

See [Liftover variants from one coordinate system to
another](../guides/genetics.html#liftover-howto) for more instructions on
lifting over a Table or MatrixTable.

Notes

This function requires the reference genome of x has a chain file loaded for
dest_reference_genome. Use
[`ReferenceGenome.add_liftover()`](../genetics/hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome.add_liftover
"hail.genetics.ReferenceGenome.add_liftover") to load and attach a chain file
to a reference genome.

Returns `None` if x could not be converted.

Warning

Before using the result of `liftover()` as a new row key or column key, be
sure to filter out missing values.

Parameters:

    

  * **x** (```Expression``` of type ```tlocus``` or ```tinterval``` of ```tlocus```) – Locus or locus interval to lift over.

  * **dest_reference_genome** (```str``` or ```ReferenceGenome```) – Reference genome to convert to.

  * **min_match** (```float```) – Minimum ratio of bases that must remap.

  * **include_strand** (```bool```) – If True, output the result as a ```StructExpression``` with the first field result being the locus or locus interval and the second field is_negative_strand is a boolean indicating whether the locus or locus interval has been mapped to the negative strand of the destination reference genome. Otherwise, output the converted locus or locus interval.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") – A locus or locus interval converted to
dest_reference_genome.

hail.expr.functions.min_rep(_locus_ ,
_alleles_)[[source]](../_modules/hail/expr/functions.html#min_rep)

    

Computes the minimal representation of a (locus, alleles) polymorphism.

Examples

    
    
    >>> hl.eval(hl.min_rep(hl.locus('1', 100000), ['TAA', 'TA']))
    Struct(locus=Locus(contig=1, position=100000, reference_genome=GRCh37), alleles=['TA', 'T'])
    
    
    
    >>> hl.eval(hl.min_rep(hl.locus('1', 100000), ['AATAA', 'AACAA']))
    Struct(locus=Locus(contig=1, position=100002, reference_genome=GRCh37), alleles=['T', 'C'])
    

Notes

Computing the minimal representation can cause the locus shift right (the
position can increase).

Parameters:

    

  * **locus** (```LocusExpression```)

  * **alleles** (```ArrayExpression``` of type ```tstr```)

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, locus
([`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression")) and alleles
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") of type
```tstr```).

hail.expr.functions.reverse_complement(_s_ , _rna
=False_)[[source]](../_modules/hail/expr/functions.html#reverse_complement)

    

Reverses the string and translates base pairs into their complements ..
rubric:: Examples

    
    
    >>> bases = hl.literal('NNGATTACA')
    >>> hl.eval(hl.reverse_complement(bases))
    'TGTAATCNN'
    

Parameters:

    

  * **s** (```StringExpression```) – Base string.

  * **rna** (```bool```) – If `True`, pair adenine (A) with uracil (U) instead of thymine (T).

Returns:

    

[`StringExpression`](../hail.expr.StringExpression.html#hail.expr.StringExpression
"hail.expr.StringExpression")

---

# Locus

_class
_hail.genetics.Locus[[source]](../_modules/hail/genetics/locus.html#Locus)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

An object that represents a location in the genome.

Parameters:

    

  * **contig** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Chromosome identifier.

  * **position** ([`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Chromosomal position (1-indexed).

  * **reference_genome** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```ReferenceGenome```) – Reference genome to use.

Note

This object refers to the Python value returned by taking or collecting Hail
expressions, e.g. `mt.locus.take(5)`. This is rare; it is much more common to
manipulate the
[`LocusExpression`](../hail.expr.LocusExpression.html#hail.expr.LocusExpression
"hail.expr.LocusExpression") object, which is constructed using the following
functions:

>   * [`locus()`](../functions/genetics.html#hail.expr.functions.locus
> "hail.expr.functions.locus")
>
>   *
> [`parse_locus()`](../functions/genetics.html#hail.expr.functions.parse_locus
> "hail.expr.functions.parse_locus")
>
>   *
> [`locus_from_global_position()`](../functions/genetics.html#hail.expr.functions.locus_from_global_position
> "hail.expr.functions.locus_from_global_position")
>
>

Attributes

`contig` | Chromosome identifier.  
---|---  
`position` | Chromosomal position (1-based).  
`reference_genome` | Reference genome.  
  
Methods

`parse` | Parses a locus object from a CHR:POS string.  
---|---  
  
_property _contig

    

Chromosome identifier. :rtype: str

_classmethod _parse(_string_ , _reference_genome
='default'_)[[source]](../_modules/hail/genetics/locus.html#Locus.parse)

    

Parses a locus object from a CHR:POS string.

**Examples**

    
    
    >>> l1 = hl.Locus.parse('1:101230')
    >>> l2 = hl.Locus.parse('X:4201230')
    

Parameters:

    

  * **string** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – String to parse.

  * **reference_genome** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or ```ReferenceGenome```) – Reference genome to use. Default is ```default_reference()```.

Return type:

    

`Locus`

_property _position

    

Chromosomal position (1-based). :rtype: int

_property _reference_genome

    

Reference genome.

Returns:

    

[`ReferenceGenome`](hail.genetics.ReferenceGenome.html#hail.genetics.ReferenceGenome
"hail.genetics.ReferenceGenome")

---

# ReferenceGenome

_class
_hail.genetics.ReferenceGenome[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

An object that represents a [reference
genome](https://en.wikipedia.org/wiki/Reference_genome).

Examples

    
    
    >>> contigs = ["1", "X", "Y", "MT"]
    >>> lengths = {"1": 249250621, "X": 155270560, "Y": 59373566, "MT": 16569}
    >>> par = [("X", 60001, 2699521)]
    >>> my_ref = hl.ReferenceGenome("my_ref", contigs, lengths, "X", "Y", "MT", par)
    

Notes

Hail comes with predefined reference genomes (case sensitive!):

>   * GRCh37, Genome Reference Consortium Human Build 37
>
>   * GRCh38, Genome Reference Consortium Human Build 38
>
>   * GRCm38, Genome Reference Consortium Mouse Build 38
>
>   * CanFam3, Canis lupus familiaris (dog)
>
>

You can access these reference genome objects using
```get_reference()```:

    
    
    >>> rg = hl.get_reference('GRCh37')
    >>> rg = hl.get_reference('GRCh38')
    >>> rg = hl.get_reference('GRCm38')
    >>> rg = hl.get_reference('CanFam3')
    

Note that constructing a new reference genome, either by using the class
constructor or by using read will add the reference genome to the list of
known references; it is possible to access the reference genome using
```get_reference()```
anytime afterwards.

Note

Reference genome names must be unique. It is not possible to overwrite the
built-in reference genomes.

Note

Hail allows setting a default reference so that the `reference_genome`
argument of [`import_vcf()`](../methods/impex.html#hail.methods.import_vcf
"hail.methods.import_vcf") does not need to be used constantly. It is a
current limitation of Hail that a custom reference genome cannot be used as
the `default_reference` argument of [`init()`](../api.html#hail.init
"hail.init"). In order to set a custom reference genome as default, pass the
reference as an argument to
[`default_reference()`](../api.html#hail.default_reference
"hail.default_reference") after initializing Hail.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name of reference. Must be unique and NOT one of Hail’s predefined references: `'GRCh37'`, `'GRCh38'`, `'GRCm38'`, `'CanFam3'` and `'default'`.

  * **contigs** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contig names.

  * **lengths** ([`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") to [`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python v3.9\)")) – Dict of contig names to contig lengths.

  * **x_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as X chromosomes.

  * **y_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as Y chromosomes.

  * **mt_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as mitochondrial DNA.

  * **par** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") of (str, int, int)) – List of tuples with (contig, start, end)

Attributes

`contigs` | Contig names.  
---|---  
`global_positions_dict` | Get a dictionary mapping contig names to their global genomic positions.  
`lengths` | Dict of contig name to contig length.  
`mt_contigs` | Mitochondrial contigs.  
`name` | Name of reference genome.  
`par` | Pseudoautosomal regions.  
`x_contigs` | X contigs.  
`y_contigs` | Y contigs.  
  
Methods

`add_liftover` | Register a chain file for liftover.  
---|---  
`add_sequence` | Load the reference sequence from a FASTA file.  
`contig_length` | Contig length.  
`from_fasta_file` | Create reference genome from a FASTA file.  
`has_liftover` | `True` if a liftover chain file is available from this reference genome to the destination reference.  
`has_sequence` | True if the reference sequence has been loaded.  
`locus_from_global_position` | "  
`read` | Load reference genome from a JSON file.  
`remove_liftover` | Remove liftover to dest_reference_genome.  
`remove_sequence` | Remove the reference sequence.  
`write` | "Write this reference genome to a file in JSON format.  
  
add_liftover(_chain_file_ ,
_dest_reference_genome_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.add_liftover)

    

Register a chain file for liftover.

Examples

Access GRCh37 and GRCh38 using
```get_reference()```:

    
    
    >>> rg37 = hl.get_reference('GRCh37') 
    >>> rg38 = hl.get_reference('GRCh38') 
    

Add a chain file from 37 to 38:

    
    
    >>> rg37.add_liftover('gs://hail-common/references/grch37_to_grch38.over.chain.gz', rg38) 
    

Notes

This method can only be run once per reference genome. Use `has_liftover()` to
test whether a chain file has been registered.

The chain file format is described
[here](https://genome.ucsc.edu/goldenpath/help/chain.html).

Chain files are hosted on google cloud for some of Hail’s built-in references:

**GRCh37 to GRCh38** gs://hail-
common/references/grch37_to_grch38.over.chain.gz

**GRCh38 to GRCh37** gs://hail-
common/references/grch38_to_grch37.over.chain.gz

Public download links are available
[here](https://console.cloud.google.com/storage/browser/hail-
common/references/).

Parameters:

    

  * **chain_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to chain file. Can be compressed (GZIP) or uncompressed.

  * **dest_reference_genome** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or `ReferenceGenome`) – Reference genome to convert to.

add_sequence(_fasta_file_ , _index_file
=None_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.add_sequence)

    

Load the reference sequence from a FASTA file.

Examples

Access the GRCh37 reference genome using
```get_reference()```:

    
    
    >>> rg = hl.get_reference('GRCh37') 
    

Add a sequence file:

    
    
    >>> rg.add_sequence('gs://hail-common/references/human_g1k_v37.fasta.gz',
    ...                 'gs://hail-common/references/human_g1k_v37.fasta.fai') 
    

Add a sequence file with the default index location:

    
    
    >>> rg.add_sequence('gs://hail-common/references/human_g1k_v37.fasta.gz') 
    

Notes

This method can only be run once per reference genome. Use `has_sequence()` to
test whether a sequence is loaded.

FASTA and index files are hosted on google cloud for some of Hail’s built-in
references:

**GRCh37**

  * FASTA file: `gs://hail-common/references/human_g1k_v37.fasta.gz`

  * Index file: `gs://hail-common/references/human_g1k_v37.fasta.fai`

**GRCh38**

  * FASTA file: `gs://hail-common/references/Homo_sapiens_assembly38.fasta.gz`

  * Index file: `gs://hail-common/references/Homo_sapiens_assembly38.fasta.fai`

Public download links are available
[here](https://console.cloud.google.com/storage/browser/hail-
common/references/).

Parameters:

    

  * **fasta_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA file. Can be compressed (GZIP) or uncompressed.

  * **index_file** ([`None`](https://docs.python.org/3.9/library/constants.html#None "\(in Python v3.9\)") or [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA index file. Must be uncompressed. If None, replace the fasta_file’s extension with fai.

contig_length(_contig_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.contig_length)

    

Contig length.

Parameters:

    

**contig** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Contig name.

Returns:

    

[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)") – Length of contig.

_property _contigs

    

Contig names.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

_classmethod _from_fasta_file(_name_ , _fasta_file_ , _index_file_ ,
_x_contigs =[]_, _y_contigs =[]_, _mt_contigs =[]_, _par
=[]_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.from_fasta_file)

    

Create reference genome from a FASTA file.

Parameters:

    

  * **name** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Name for new reference genome.

  * **fasta_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA file. Can be compressed (GZIP) or uncompressed.

  * **index_file** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Path to FASTA index file. Must be uncompressed.

  * **x_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as X chromosomes.

  * **y_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as Y chromosomes.

  * **mt_contigs** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") or [`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Contigs to be treated as mitochondrial DNA.

  * **par** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of [`tuple`](https://docs.python.org/3.9/library/stdtypes.html#tuple "\(in Python v3.9\)") of (str, int, int)) – List of tuples with (contig, start, end)

Returns:

    

`ReferenceGenome`

_property _global_positions_dict

    

Get a dictionary mapping contig names to their global genomic positions.

Returns:

    

[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)") – A dictionary of contig names to global genomic positions.

has_liftover(_dest_reference_genome_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.has_liftover)

    

`True` if a liftover chain file is available from this reference genome to the
destination reference.

Parameters:

    

**dest_reference_genome**
([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or `ReferenceGenome`)

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

has_sequence()[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.has_sequence)

    

True if the reference sequence has been loaded.

Returns:

    

[`bool`](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

_property _lengths

    

Dict of contig name to contig length.

Returns:

    

[`dict`](https://docs.python.org/3.9/library/stdtypes.html#dict "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)") to
[`int`](https://docs.python.org/3.9/library/functions.html#int "\(in Python
v3.9\)")

locus_from_global_position(_global_pos_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.locus_from_global_position)

    

” Constructs a locus from a global position in reference genome. The inverse
of [`Locus.position()`](hail.genetics.Locus.html#hail.genetics.Locus.position
"hail.genetics.Locus.position").

Examples

    
    
    >>> rg = hl.get_reference('GRCh37')
    >>> rg.locus_from_global_position(0)
    Locus(contig=1, position=1, reference_genome=GRCh37)
    
    
    
    >>> rg.locus_from_global_position(2824183054)
    Locus(contig=21, position=42584230, reference_genome=GRCh37)
    
    
    
    >>> rg = hl.get_reference('GRCh38')
    >>> rg.locus_from_global_position(2824183054)
    Locus(contig=chr22, position=1, reference_genome=GRCh38)
    

Parameters:

    

**global_pos** (_int_) – Zero-based global base position along the reference
genome.

Returns:

    

```Locus```

_property _mt_contigs

    

Mitochondrial contigs.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

_property _name

    

Name of reference genome.

Returns:

    

[`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")

_property _par

    

Pseudoautosomal regions.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`Interval`](../utils/index.html#hail.utils.Interval
"hail.utils.Interval")

_classmethod
_read(_path_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.read)

    

Load reference genome from a JSON file.

Notes

The JSON file must have the following format:

    
    
    {"name": "my_reference_genome",
     "contigs": [{"name": "1", "length": 10000000},
                 {"name": "2", "length": 20000000},
                 {"name": "X", "length": 19856300},
                 {"name": "Y", "length": 78140000},
                 {"name": "MT", "length": 532}],
     "xContigs": ["X"],
     "yContigs": ["Y"],
     "mtContigs": ["MT"],
     "par": [{"start": {"contig": "X","position": 60001},"end": {"contig": "X","position": 2699521}},
             {"start": {"contig": "Y","position": 10001},"end": {"contig": "Y","position": 2649521}}]
    }
    

name must be unique and not overlap with Hail’s pre-instantiated references:
`'GRCh37'`, `'GRCh38'`, `'GRCm38'`, `'CanFam3'`, and `'default'`. The contig
names in xContigs, yContigs, and mtContigs must be present in contigs. The
intervals listed in par must have contigs in either xContigs or yContigs and
must have positions between 0 and the contig length given in contigs.

Parameters:

    

**path** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in
Python v3.9\)")) – Path to JSON file.

Returns:

    

`ReferenceGenome`

remove_liftover(_dest_reference_genome_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.remove_liftover)

    

Remove liftover to dest_reference_genome.

Parameters:

    

**dest_reference_genome**
([`str`](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or `ReferenceGenome`)

remove_sequence()[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.remove_sequence)

    

Remove the reference sequence.

write(_output_)[[source]](../_modules/hail/genetics/reference_genome.html#ReferenceGenome.write)

    

“Write this reference genome to a file in JSON format.

Examples

    
    
    >>> my_rg = hl.ReferenceGenome("new_reference", ["x", "y", "z"], {"x": 500, "y": 300, "z": 200})
    >>> my_rg.write(f"output/new_reference.json")
    

Notes

Use `read()` to reimport the exported reference genome in a new HailContext
session.

Parameters:

    

**output** ([`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")) – Path of JSON file to write.

_property _x_contigs

    

X contigs.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

_property _y_contigs

    

Y contigs.

Returns:

    

[`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python
v3.9\)") of [`str`](https://docs.python.org/3.9/library/stdtypes.html#str
"\(in Python v3.9\)")

---

# Numeric functions

Numeric functions

`abs`(x) | Take the absolute value of a numeric value, array or ndarray.  
---|---  
`approx_equal`(x, y[, tolerance, absolute, ...]) | Tests whether two numbers are approximately equal.  
`bit_and`(x, y) | Bitwise and x and y.  
`bit_or`(x, y) | Bitwise or x and y.  
`bit_xor`(x, y) | Bitwise exclusive-or x and y.  
`bit_lshift`(x, y) | Bitwise left-shift x by y.  
`bit_rshift`(x, y[, logical]) | Bitwise right-shift x by y.  
`bit_not`(x) | Bitwise invert x.  
`bit_count`(x) | Count the number of 1s in the in the [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) binary representation of x.  
`exp`(x) |   
`expit`(x) |   
`is_nan`(x) |   
`is_finite`(x) |   
`is_infinite`(x) |   
`log`(x[, base]) | Take the logarithm of the x with base base.  
`log10`(x) |   
`logit`(x) |   
`sign`(x) | Returns the sign of a numeric value, array or ndarray.  
`sqrt`(x) |   
```int```(x) | Convert to a 32-bit integer expression.  
```int32```(x) | Convert to a 32-bit integer expression.  
```int64```(x) | Convert to a 64-bit integer expression.  
```float```(x) | Convert to a 64-bit floating point expression.  
```float32```(x) | Convert to a 32-bit floating point expression.  
```float64```(x) | Convert to a 64-bit floating point expression.  
`floor`(x) |   
`ceil`(x) |   
`uniroot`(f, min, max, *[, max_iter, epsilon, ...]) | Finds a root of the function f within the interval [min, max].  
  
Numeric collection functions

`min`(*exprs[, filter_missing]) | Returns the minimum element of a collection or of given numeric expressions.  
---|---  
`nanmin`(*exprs[, filter_missing]) | Returns the minimum value of a collection or of given arguments, excluding NaN.  
`max`(*exprs[, filter_missing]) | Returns the maximum element of a collection or of given numeric expressions.  
`nanmax`(*exprs[, filter_missing]) | Returns the maximum value of a collection or of given arguments, excluding NaN.  
`mean`(collection[, filter_missing]) | Returns the mean of all values in the collection.  
`median`(collection) | Returns the median value in the collection.  
`product`(collection[, filter_missing]) | Returns the product of values in the collection.  
`sum`(collection[, filter_missing]) | Returns the sum of values in the collection.  
`cumulative_sum`(a[, filter_missing]) | Returns an array of the cumulative sum of values in the array.  
`argmin`(array[, unique]) | Return the index of the minimum value in the array.  
`argmax`(array[, unique]) | Return the index of the maximum value in the array.  
`corr`(x, y) | Compute the [Pearson correlation coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) between x and y.  
`binary_search`(array, elem) | Binary search array for the insertion point of elem.  
  
hail.expr.functions.abs(_x_)[[source]](../_modules/hail/expr/functions.html#abs)

    

Take the absolute value of a numeric value, array or ndarray.

Examples

    
    
    >>> hl.eval(hl.abs(-5))
    5
    
    
    
    >>> hl.eval(hl.abs([1.0, -2.5, -5.1]))
    [1.0, 2.5, 5.1]
    

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression").

hail.expr.functions.approx_equal(_x_ , _y_ , _tolerance =1e-06_, _absolute
=False_, _nan_same
=False_)[[source]](../_modules/hail/expr/functions.html#approx_equal)

    

Tests whether two numbers are approximately equal.

Examples

    
    
    >>> hl.eval(hl.approx_equal(0.25, 0.2500001))
    True
    
    
    
    >>> hl.eval(hl.approx_equal(0.25, 0.251, tolerance=1e-3, absolute=True))
    False
    

Parameters:

    

  * **x** (```NumericExpression```)

  * **y** (```NumericExpression```)

  * **tolerance** (```NumericExpression```)

  * **absolute** (```BooleanExpression```) – If True, compute `abs(x - y) <= tolerance`. Otherwise, compute `abs(x - y) <= max(tolerance * max(abs(x), abs(y)), 2 ** -1022)`.

  * **nan_same** (```BooleanExpression```) – If True, then `NaN == NaN` will evaluate to True. Otherwise, it will return False.

Returns:

    

[`BooleanExpression`](../hail.expr.BooleanExpression.html#hail.expr.BooleanExpression
"hail.expr.BooleanExpression")

hail.expr.functions.bit_and(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_and)

    

Bitwise and x and y.

Examples

    
    
    >>> hl.eval(hl.bit_and(5, 3))
    1
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_or(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_or)

    

Bitwise or x and y.

Examples

    
    
    >>> hl.eval(hl.bit_or(5, 3))
    7
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_xor(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_xor)

    

Bitwise exclusive-or x and y.

Examples

    
    
    >>> hl.eval(hl.bit_xor(5, 3))
    6
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_lshift(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#bit_lshift)

    

Bitwise left-shift x by y.

Examples

    
    
    >>> hl.eval(hl.bit_lshift(5, 3))
    40
    
    
    
    >>> hl.eval(hl.bit_lshift(1, 8))
    256
    

Unlike Python, Hail integers are fixed-size (32 or 64 bits), and bits extended
beyond will be ignored:

    
    
    >>> hl.eval(hl.bit_lshift(1, 31))
    -2147483648
    
    
    
    >>> hl.eval(hl.bit_lshift(1, 32))
    0
    
    
    
    >>> hl.eval(hl.bit_lshift(hl.int64(1), 32))
    4294967296
    
    
    
    >>> hl.eval(hl.bit_lshift(hl.int64(1), 64))
    0
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_rshift(_x_ , _y_ , _logical
=False_)[[source]](../_modules/hail/expr/functions.html#bit_rshift)

    

Bitwise right-shift x by y.

Examples

    
    
    >>> hl.eval(hl.bit_rshift(256, 3))
    32
    

With `logical=False` (default), the sign is preserved:

    
    
    >>> hl.eval(hl.bit_rshift(-1, 1))
    -1
    

With `logical=True`, the sign bit is treated as any other:

    
    
    >>> hl.eval(hl.bit_rshift(-1, 1, logical=True))
    2147483647
    

Notes

If logical is `False`, then the shift is a sign-preserving right shift. If
logical is `True`, then the shift is logical, with the sign bit treated as any
other bit.

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

  * **x** (```Int32Expression``` or ```Int64Expression```)

  * **y** (```Int32Expression``` or ```Int64Expression```)

  * **logical** (```bool```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_not(_x_)[[source]](../_modules/hail/expr/functions.html#bit_not)

    

Bitwise invert x.

Examples

    
    
    >>> hl.eval(hl.bit_not(0))
    -1
    

Notes

See [the Python wiki](https://wiki.python.org/moin/BitwiseOperators) for more
information about bit operators.

Parameters:

    

**x**
([`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression"))

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression")

hail.expr.functions.bit_count(_x_)[[source]](../_modules/hail/expr/functions.html#bit_count)

    

Count the number of 1s in the in the [two’s
complement](https://en.wikipedia.org/wiki/Two%27s_complement) binary
representation of x.

Examples

The binary representation of 7 is 111, so:

    
    
    >>> hl.eval(hl.bit_count(7))
    3
    

Parameters:

    

**x**
([`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression") or
[`Int64Expression`](../hail.expr.Int64Expression.html#hail.expr.Int64Expression
"hail.expr.Int64Expression"))

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

hail.expr.functions.exp(_x_)[[source]](../_modules/hail/expr/functions.html#exp)

    

hail.expr.functions.expit(_x_)[[source]](../_modules/hail/expr/functions.html#expit)

    

hail.expr.functions.is_nan(_x_)[[source]](../_modules/hail/expr/functions.html#is_nan)

    

hail.expr.functions.is_finite(_x_)[[source]](../_modules/hail/expr/functions.html#is_finite)

    

hail.expr.functions.is_infinite(_x_)[[source]](../_modules/hail/expr/functions.html#is_infinite)

    

hail.expr.functions.log(_x_ , _base
=None_)[[source]](../_modules/hail/expr/functions.html#log)

    

Take the logarithm of the x with base base.

Examples

    
    
    >>> hl.eval(hl.log(10))
    2.302585092994046
    
    
    
    >>> hl.eval(hl.log(10, 10))
    1.0
    
    
    
    >>> hl.eval(hl.log(1024, 2))
    10.0
    

Notes

If the base argument is not supplied, then the natural logarithm is used.

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **base** (float or ```Expression``` of type ```tfloat64```)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.log10(_x_)[[source]](../_modules/hail/expr/functions.html#log10)

    

hail.expr.functions.logit(_x_)[[source]](../_modules/hail/expr/functions.html#logit)

    

hail.expr.functions.floor(_x_)[[source]](../_modules/hail/expr/functions.html#floor)

    

hail.expr.functions.ceil(_x_)[[source]](../_modules/hail/expr/functions.html#ceil)

    

hail.expr.functions.sqrt(_x_)[[source]](../_modules/hail/expr/functions.html#sqrt)

    

hail.expr.functions.sign(_x_)[[source]](../_modules/hail/expr/functions.html#sign)

    

Returns the sign of a numeric value, array or ndarray.

Examples

    
    
    >>> hl.eval(hl.sign(-1.23))
    -1.0
    
    
    
    >>> hl.eval(hl.sign([-4, 0, 5]))
    [-1, 0, 1]
    
    
    
    >>> hl.eval(hl.sign([0.0, 3.14]))
    [0.0, 1.0]
    
    
    
    >>> hl.eval(hl.sign(float('nan')))
    nan
    

Notes

The sign function preserves type and maps `nan` to `nan`.

Parameters:

    

**x**
([`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression"))

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression"),
[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression") or
[`NDArrayNumericExpression`](../hail.expr.NDArrayNumericExpression.html#hail.expr.NDArrayNumericExpression
"hail.expr.NDArrayNumericExpression").

hail.expr.functions.min(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#min)

    

Returns the minimum element of a collection or of given numeric expressions.

Examples

Take the minimum value of an array:

    
    
    >>> hl.eval(hl.min([1, 3, 5, 6, 7, 9]))
    1
    

Take the minimum value of arguments:

    
    
    >>> hl.eval(hl.min(1, 50, 2))
    1
    

Notes

Like the Python builtin `min` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the minimum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

If any element or argument is NaN, then the result is NaN.

See also

`nanmin()`, `max()`, `nanmax()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing minimum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.nanmin(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#nanmin)

    

Returns the minimum value of a collection or of given arguments, excluding
NaN.

Examples

Compute the minimum value of an array:

    
    
    >>> hl.eval(hl.nanmin([1.1, 50.1, float('nan')]))
    1.1
    

Take the minimum value of arguments:

    
    
    >>> hl.eval(hl.nanmin(1.1, 50.1, float('nan')))
    1.1
    

Notes

Like the Python builtin `min` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the minimum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

NaN arguments / array elements are ignored; the minimum value of NaN and any
non-NaN value x is x.

See also

`min()`, `max()`, `nanmax()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing minimum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.max(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#max)

    

Returns the maximum element of a collection or of given numeric expressions.

Examples

Take the maximum value of an array:

    
    
    >>> hl.eval(hl.max([1, 3, 5, 6, 7, 9]))
    9
    

Take the maximum value of values:

    
    
    >>> hl.eval(hl.max(1, 50, 2))
    50
    

Notes

Like the Python builtin `max` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the maximum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

If any element or argument is NaN, then the result is NaN.

See also

`nanmax()`, `min()`, `nanmin()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing maximum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.nanmax(_* exprs_, _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#nanmax)

    

Returns the maximum value of a collection or of given arguments, excluding
NaN.

Examples

Compute the maximum value of an array:

    
    
    >>> hl.eval(hl.nanmax([1.1, 50.1, float('nan')]))
    50.1
    

Take the maximum value of arguments:

    
    
    >>> hl.eval(hl.nanmax(1.1, 50.1, float('nan')))
    50.1
    

Notes

Like the Python builtin `max` function, this function can either take a single
iterable expression (an array or set of numeric elements), or variable-length
arguments of numeric expressions.

Note

If filter_missing is `True`, then the result is the maximum of non-missing
arguments or elements. If filter_missing is `False`, then any missing argument
or element causes the result to be missing.

NaN arguments / array elements are ignored; the maximum value of NaN and any
non-NaN value x is x.

See also

`max()`, `min()`, `nanmin()`

Parameters:

    

  * **exprs** (```ArrayExpression``` or ```SetExpression``` or varargs of ```NumericExpression```) – Single numeric array or set, or multiple numeric values.

  * **filter_missing** (```bool```) – Remove missing arguments/elements before computing maximum.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.mean(_collection_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#mean)

    

Returns the mean of all values in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.mean(a))
    5.166666666666667
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.median(_collection_)[[source]](../_modules/hail/expr/functions.html#median)

    

Returns the median value in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.median(a))
    5
    

Note

Missing elements are ignored.

Parameters:

    

**collection**
([`ArrayExpression`](../hail.expr.ArrayExpression.html#hail.expr.ArrayExpression
"hail.expr.ArrayExpression") or
[`SetExpression`](../hail.expr.SetExpression.html#hail.expr.SetExpression
"hail.expr.SetExpression")) – Collection expression with numeric element type.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.product(_collection_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#product)

    

Returns the product of values in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.product(a))
    5670
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.sum(_collection_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#sum)

    

Returns the sum of values in the collection.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.sum(a))
    31
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **collection** (```ArrayExpression``` or ```SetExpression```) – Collection expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`NumericExpression`](../hail.expr.NumericExpression.html#hail.expr.NumericExpression
"hail.expr.NumericExpression")

hail.expr.functions.cumulative_sum(_a_ , _filter_missing
=True_)[[source]](../_modules/hail/expr/functions.html#cumulative_sum)

    

Returns an array of the cumulative sum of values in the array.

Examples

    
    
    >>> a = [1, 3, 5, 6, 7, 9]
    
    
    
    >>> hl.eval(hl.cumulative_sum(a))
    [1, 4, 9, 15, 22, 31]
    

Note

Missing elements are ignored if filter_missing is `True`. If filter_missing is
`False`, then any missing element causes the result to be missing.

Parameters:

    

  * **a** (```ArrayNumericExpression```) – Array expression with numeric element type.

  * **filter_missing** (```bool```) – Remove missing elements from the collection before computing product.

Returns:

    

[`ArrayNumericExpression`](../hail.expr.ArrayNumericExpression.html#hail.expr.ArrayNumericExpression
"hail.expr.ArrayNumericExpression")

hail.expr.functions.argmin(_array_ , _unique
=False_)[[source]](../_modules/hail/expr/functions.html#argmin)

    

Return the index of the minimum value in the array.

Examples

    
    
    >>> hl.eval(hl.argmin([0.2, 0.3, 0.6]))
    0
    
    
    
    >>> hl.eval(hl.argmin([0.4, 0.2, 0.2]))
    1
    
    
    
    >>> hl.eval(hl.argmin([0.4, 0.2, 0.2], unique=True))
    None
    

Notes

Returns the index of the minimum value in the array.

If two or more elements are tied for minimum, then the unique parameter will
determine the result. If unique is `False`, then the first index will be
returned. If unique is `True`, then the result is missing.

If the array is empty, then the result is missing.

Note

Missing elements are ignored.

Parameters:

    

  * **array** (```ArrayNumericExpression```)

  * **unique** (_bool_)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.argmax(_array_ , _unique
=False_)[[source]](../_modules/hail/expr/functions.html#argmax)

    

Return the index of the maximum value in the array.

Examples

    
    
    >>> hl.eval(hl.argmax([0.2, 0.2, 0.6]))
    2
    
    
    
    >>> hl.eval(hl.argmax([0.4, 0.4, 0.2]))
    0
    
    
    
    >>> hl.eval(hl.argmax([0.4, 0.4, 0.2], unique=True))
    None
    

Notes

Returns the index of the maximum value in the array.

If two or more elements are tied for maximum, then the unique parameter will
determine the result. If unique is `False`, then the first index will be
returned. If unique is `True`, then the result is missing.

If the array is empty, then the result is missing.

Note

Missing elements are ignored.

Parameters:

    

  * **array** (```ArrayNumericExpression```)

  * **unique** (_bool_)

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
```tint32```

hail.expr.functions.corr(_x_ ,
_y_)[[source]](../_modules/hail/expr/functions.html#corr)

    

Compute the [Pearson correlation
coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)
between x and y.

Examples

    
    
    >>> hl.eval(hl.corr([1, 2, 4], [2, 3, 1]))
    -0.6546536707079772
    

Notes

Only indices where both x and y are non-missing will be included in the
calculation.

If x and y have length zero, then the result is missing.

Parameters:

    

  * **x** (```Expression``` of type `array<tfloat64>`)

  * **y** (```Expression``` of type `array<tfloat64>`)

Returns:

    

[`Float64Expression`](../hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression")

hail.expr.functions.uniroot(_f_ , _min_ , _max_ , _*_ , _max_iter =1000_,
_epsilon =2.220446049250313e-16_, _tolerance
=0.0001220703_)[[source]](../_modules/hail/expr/functions.html#uniroot)

    

Finds a root of the function f within the interval [min, max].

Examples

    
    
    >>> hl.eval(hl.uniroot(lambda x: x - 1, -5, 5))
    1.0
    

Notes

f(min) and f(max) must not have the same sign.

If no root can be found, the result of this call will be NA (missing).

`uniroot()` returns an estimate for a root with accuracy 4 * epsilon * abs(x)
+ tolerance.

4*EPSILON*abs(x) + tol

Parameters:

    

  * **f** (function ( (arg) -> ```Float64Expression```)) – Must return a ```Float64Expression```.

  * **min** (```Float64Expression```)

  * **max** (```Float64Expression```)

  * **max_iter** (int) – The maximum number of iterations before giving up.

  * **epsilon** (float) – The scaling factor in the accuracy of the root found.

  * **tolerance** (float) – The constant factor in approximate accuracy of the root found.

Returns:

    

[`Float64Expression`](../hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression") – The root of the function f.

hail.expr.functions.binary_search(_array_ ,
_elem_)[[source]](../_modules/hail/expr/functions.html#binary_search)

    

Binary search array for the insertion point of elem.

Parameters:

    

  * **array** (```Expression``` of type ```tarray```)

  * **elem** (```Expression```)

Returns:

    

[`Int32Expression`](../hail.expr.Int32Expression.html#hail.expr.Int32Expression
"hail.expr.Int32Expression")

Notes

This function assumes that array is sorted in ascending order, and does not
perform any sortedness check. Missing values sort last.

The returned index is the lower bound on the insertion point of elem into the
ordered array, or the index of the first element in array not smaller than
elem. This is a value between 0 and the length of array, inclusive (if all
elements in array are smaller than elem, the returned value is the length of
array or the index of the first missing value, if one exists).

If either elem or array is missing, the result is missing.

Examples

    
    
    >>> a = hl.array([0, 2, 4, 8])
    
    
    
    >>> hl.eval(hl.binary_search(a, -1))
    0
    
    
    
    >>> hl.eval(hl.binary_search(a, 1))
    1
    
    
    
    >>> hl.eval(hl.binary_search(a, 10))
    4

---

# Statistical functions

`chi_squared_test`(c1, c2, c3, c4) | Performs chi-squared test of independence on a 2x2 contingency table.  
---|---  
`fisher_exact_test`(c1, c2, c3, c4) | Calculates the p-value, odds ratio, and 95% confidence interval using Fisher's exact test for a 2x2 table.  
`contingency_table_test`(c1, c2, c3, c4, ...) | Performs chi-squared or Fisher's exact test of independence on a 2x2 contingency table.  
`cochran_mantel_haenszel_test`(a, b, c, d) | Perform the Cochran-Mantel-Haenszel test for association.  
`dbeta`(x, a, b) | Returns the probability density at x of a [beta distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters a (alpha) and b (beta).  
`dchisq`(x, df[, ncp, log_p]) | Compute the probability density at x of a chi-squared distribution with df degrees of freedom.  
`dnorm`(x[, mu, sigma, log_p]) | Compute the probability density at x of a normal distribution with mean mu and standard deviation sigma.  
`dpois`(x, lamb[, log_p]) | Compute the (log) probability density at x of a Poisson distribution with rate parameter lamb.  
`hardy_weinberg_test`(n_hom_ref, n_het, n_hom_var) | Performs test of Hardy-Weinberg equilibrium.  
`binom_test`(x, n, p, alternative) | Performs a binomial test on p given x successes in n trials.  
`pchisqtail`(x, df[, ncp, lower_tail, log_p]) | Returns the probability under the right-tail starting at x for a chi-squared distribution with df degrees of freedom.  
`pgenchisq`(x, w, k, lam, mu, sigma, *[, ...]) | The cumulative probability function of a [generalized chi-squared distribution](https://en.wikipedia.org/wiki/Generalized_chi-squared_distribution).  
`pnorm`(x[, mu, sigma, lower_tail, log_p]) | The cumulative probability function of a normal distribution with mean mu and standard deviation sigma.  
`pT`(x, n[, lower_tail, log_p]) | The cumulative probability function of a [t-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution) with n degrees of freedom.  
`pF`(x, df1, df2[, lower_tail, log_p]) | The cumulative probability function of a [F-distribution](https://en.wikipedia.org/wiki/F-distribution) with parameters df1 and df2.  
`ppois`(x, lamb[, lower_tail, log_p]) | The cumulative probability function of a Poisson distribution.  
`qchisqtail`(p, df[, ncp, lower_tail, log_p]) | The quantile function of a chi-squared distribution with df degrees of freedom, inverts `pchisqtail()`.  
`qnorm`(p[, mu, sigma, lower_tail, log_p]) | The quantile function of a normal distribution with mean mu and standard deviation sigma, inverts `pnorm()`.  
`qpois`(p, lamb[, lower_tail, log_p]) | The quantile function of a Poisson distribution with rate parameter lamb, inverts `ppois()`.  
  
hail.expr.functions.chi_squared_test(_c1_ , _c2_ , _c3_ ,
_c4_)[[source]](../_modules/hail/expr/functions.html#chi_squared_test)

    

Performs chi-squared test of independence on a 2x2 contingency table.

Examples

    
    
    >>> hl.eval(hl.chi_squared_test(10, 10, 10, 10))
    Struct(p_value=1.0, odds_ratio=1.0)
    
    
    
    >>> hl.eval(hl.chi_squared_test(51, 43, 22, 92))
    Struct(p_value=1.4626257805267089e-07, odds_ratio=4.959830866807611)
    

Notes

The odds ratio is given by `(c1 / c2) / (c3 / c4)`.

Returned fields may be `nan` or `inf`.

Parameters:

    

  * **c1** (int or ```Expression``` of type ```tint32```) – Value for cell 1.

  * **c2** (int or ```Expression``` of type ```tint32```) – Value for cell 2.

  * **c3** (int or ```Expression``` of type ```tint32```) – Value for cell 3.

  * **c4** (int or ```Expression``` of type ```tint32```) – Value for cell 4.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and odds_ratio
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.fisher_exact_test(_c1_ , _c2_ , _c3_ ,
_c4_)[[source]](../_modules/hail/expr/functions.html#fisher_exact_test)

    

Calculates the p-value, odds ratio, and 95% confidence interval using Fisher’s
exact test for a 2x2 table.

Examples

    
    
    >>> hl.eval(hl.fisher_exact_test(10, 10, 10, 10))
    Struct(p_value=1.0000000000000002, odds_ratio=1.0,
           ci_95_lower=0.24385796914260355, ci_95_upper=4.100747675033819)
    
    
    
    >>> hl.eval(hl.fisher_exact_test(51, 43, 22, 92))
    Struct(p_value=2.1564999740157304e-07, odds_ratio=4.918058171469967,
           ci_95_lower=2.5659373368248444, ci_95_upper=9.677929632035475)
    

Notes

This method is identical to the version implemented in
[R](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/fisher.test.html)
with default parameters (two-sided, alpha = 0.05, null hypothesis that the
odds ratio equals 1).

Returned fields may be `nan` or `inf`.

Parameters:

    

  * **c1** (int or ```Expression``` of type ```tint32```) – Value for cell 1.

  * **c2** (int or ```Expression``` of type ```tint32```) – Value for cell 2.

  * **c3** (int or ```Expression``` of type ```tint32```) – Value for cell 3.

  * **c4** (int or ```Expression``` of type ```tint32```) – Value for cell 4.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with four fields, p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")), odds_ratio
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")), ci_95_lower (:py:data:.tfloat64`), and
ci_95_upper ([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.contingency_table_test(_c1_ , _c2_ , _c3_ , _c4_ ,
_min_cell_count_)[[source]](../_modules/hail/expr/functions.html#contingency_table_test)

    

Performs chi-squared or Fisher’s exact test of independence on a 2x2
contingency table.

Examples

    
    
    >>> hl.eval(hl.contingency_table_test(51, 43, 22, 92, min_cell_count=22))
    Struct(p_value=1.4626257805267089e-07, odds_ratio=4.959830866807611)
    
    
    
    >>> hl.eval(hl.contingency_table_test(51, 43, 22, 92, min_cell_count=23))
    Struct(p_value=2.1564999740157304e-07, odds_ratio=4.918058171469967)
    

Notes

If all cell counts are at least min_cell_count, the chi-squared test is used.
Otherwise, Fisher’s exact test is used.

Returned fields may be `nan` or `inf`.

Parameters:

    

  * **c1** (int or ```Expression``` of type ```tint32```) – Value for cell 1.

  * **c2** (int or ```Expression``` of type ```tint32```) – Value for cell 2.

  * **c3** (int or ```Expression``` of type ```tint32```) – Value for cell 3.

  * **c4** (int or ```Expression``` of type ```tint32```) – Value for cell 4.

  * **min_cell_count** (int or ```Expression``` of type ```tint32```) – Minimum count in every cell to use the chi-squared test.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and odds_ratio
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.cochran_mantel_haenszel_test(_a_ , _b_ , _c_ ,
_d_)[[source]](../_modules/hail/expr/functions.html#cochran_mantel_haenszel_test)

    

Perform the Cochran-Mantel-Haenszel test for association.

Examples

    
    
    >>> a = [56, 61, 73, 71]
    >>> b = [69, 257, 65, 48]
    >>> c = [40, 57, 71, 55]
    >>> d = [77, 301, 79, 48]
    >>> hl.eval(hl.cochran_mantel_haenszel_test(a, b, c, d))
    Struct(test_statistic=5.0496881823306765, p_value=0.024630370456863417)
    
    
    
    >>> mt = ds.filter_rows(mt.locus == hl.Locus(20, 10633237))
    >>> mt.count_rows()
    1
    >>> a, b, c, d = mt.aggregate_entries(
    ...     hl.tuple([
    ...         hl.array([hl.agg.count_where(mt.GT.is_non_ref() & mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(mt.GT.is_non_ref() & mt.pheno.is_case & ~mt.pheno.is_female)]),
    ...         hl.array([hl.agg.count_where(mt.GT.is_non_ref() & ~mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(mt.GT.is_non_ref() & ~mt.pheno.is_case & ~mt.pheno.is_female)]),
    ...         hl.array([hl.agg.count_where(~mt.GT.is_non_ref() & mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(~mt.GT.is_non_ref() & mt.pheno.is_case & ~mt.pheno.is_female)]),
    ...         hl.array([hl.agg.count_where(~mt.GT.is_non_ref() & ~mt.pheno.is_case & mt.pheno.is_female), hl.agg.count_where(~mt.GT.is_non_ref() & ~mt.pheno.is_case & ~mt.pheno.is_female)])
    ...     ])
    ... )
    >>> hl.eval(hl.cochran_mantel_haenszel_test(a, b, c, d))
    Struct(test_statistic=0.2188830334629822, p_value=0.6398923118508772)
    

Notes

See the [Wikipedia
article](https://en.m.wikipedia.org/wiki/Cochran%E2%80%93Mantel%E2%80%93Haenszel_statistics)
for more details.

Parameters:

    

  * **a** (```ArrayExpression``` of type ```tint64```) – Values for the upper-left cell in the contingency tables.

  * **b** (```ArrayExpression``` of type ```tint64```) – Values for the upper-right cell in the contingency tables.

  * **c** (```ArrayExpression``` of type ```tint64```) – Values for the lower-left cell in the contingency tables.

  * **d** (```ArrayExpression``` of type ```tint64```) – Values for the lower-right cell in the contingency tables.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A
```tstruct```
expression with two fields, test_statistic
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.dbeta(_x_ , _a_ ,
_b_)[[source]](../_modules/hail/expr/functions.html#dbeta)

    

Returns the probability density at x of a [beta
distribution](https://en.wikipedia.org/wiki/Beta_distribution) with parameters
a (alpha) and b (beta).

Examples

    
    
    >>> hl.eval(hl.dbeta(.2, 5, 20))
    4.900377563180943
    

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – Point in [0,1] at which to sample. If a < 1 then x must be positive. If b < 1 then x must be less than 1.

  * **a** (```float``` or ```Expression``` of type ```tfloat64```) – The alpha parameter in the beta distribution. The result is undefined for non-positive a.

  * **b** (```float``` or ```Expression``` of type ```tfloat64```) – The beta parameter in the beta distribution. The result is undefined for non-positive b.

Returns:

    

[`Float64Expression`](../hail.expr.Float64Expression.html#hail.expr.Float64Expression
"hail.expr.Float64Expression")

hail.expr.functions.dchisq(_x_ , _df_ , _ncp =None_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#dchisq)

    

Compute the probability density at x of a chi-squared distribution with df
degrees of freedom.

Examples

    
    
    >>> hl.eval(hl.dchisq(1, 2))
    0.3032653298563167
    
    
    
    >>> hl.eval(hl.dchisq(1, 2, ncp=2))
    0.17472016746112667
    
    
    
    >>> hl.eval(hl.dchisq(1, 2, log_p=True))
    -1.1931471805599454
    

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```) – Non-negative number at which to compute the probability density.

  * **df** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom.

  * **ncp** (float or ```Expression``` of type ```tfloat64```) – Noncentrality parameter, defaults to 0 if unspecified.

  * **log_p** (bool or ```BooleanExpression```) – If `True`, the natural logarithm of the probability density is returned.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – The probability density.

hail.expr.functions.dnorm(_x_ , _mu =0_, _sigma =1_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#dnorm)

    

Compute the probability density at x of a normal distribution with mean mu and
standard deviation sigma. Returns density of standard normal distribution by
default.

Examples

    
    
    >>> hl.eval(hl.dnorm(1))
    0.24197072451914337
    
    
    
    >>> hl.eval(hl.dnorm(1, mu=1, sigma=2))
    0.19947114020071635
    
    
    
    >>> hl.eval(hl.dnorm(1, log_p=True))
    -1.4189385332046727
    

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – Real number at which to compute the probability density.

  * **mu** (float or ```Expression``` of type ```tfloat64```) – Mean (default = 0).

  * **sigma** (float or ```Expression``` of type ```tfloat64```) – Standard deviation (default = 1).

  * **log_p** (```bool``` or ```BooleanExpression```) – If `True`, the natural logarithm of the probability density is returned.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – The probability density.

hail.expr.functions.dpois(_x_ , _lamb_ , _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#dpois)

    

Compute the (log) probability density at x of a Poisson distribution with rate
parameter lamb.

Examples

    
    
    >>> hl.eval(hl.dpois(5, 3))
    0.10081881344492458
    

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – Non-negative number at which to compute the probability density.

  * **lamb** (```float``` or ```Expression``` of type ```tfloat64```) – Poisson rate parameter. Must be non-negative.

  * **log_p** (```bool``` or ```BooleanExpression```) – If `True`, the natural logarithm of the probability density is returned.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – The (log) probability density.

hail.expr.functions.hardy_weinberg_test(_n_hom_ref_ , _n_het_ , _n_hom_var_ ,
_one_sided
=False_)[[source]](../_modules/hail/expr/functions.html#hardy_weinberg_test)

    

Performs test of Hardy-Weinberg equilibrium.

Examples

    
    
    >>> hl.eval(hl.hardy_weinberg_test(250, 500, 250))
    Struct(het_freq_hwe=0.5002501250625313, p_value=0.9747844394217698)
    
    
    
    >>> hl.eval(hl.hardy_weinberg_test(37, 200, 85))
    Struct(het_freq_hwe=0.48964964307448583, p_value=1.1337210383168987e-06)
    

Notes

By default, this method performs a two-sided exact test with mid-p-value
correction of [Hardy-Weinberg
equilibrium](https://en.wikipedia.org/wiki/Hardy%E2%80%93Weinberg_principle)
via an efficient implementation of the [Levene-Haldane
distribution](../_static/LeveneHaldane.pdf), which models the number of
heterozygous individuals under equilibrium.

The mean of this distribution is `(n_ref * n_var) / (2n - 1)`, where `n_ref =
2*n_hom_ref + n_het` is the number of reference alleles, `n_var = 2*n_hom_var
+ n_het` is the number of variant alleles, and `n = n_hom_ref + n_het +
n_hom_var` is the number of individuals. So the expected frequency of
heterozygotes under equilibrium, het_freq_hwe, is this mean divided by `n`.

To perform one-sided exact test of excess heterozygosity with mid-p-value
correction instead, set one_sided=True and the p-value returned will be from
the one-sided exact test.

Parameters:

    

  * **n_hom_ref** (int or ```Expression``` of type ```tint32```) – Number of homozygous reference genotypes.

  * **n_het** (int or ```Expression``` of type ```tint32```) – Number of heterozygous genotypes.

  * **n_hom_var** (int or ```Expression``` of type ```tint32```) – Number of homozygous variant genotypes.

  * **one_sided** (```bool```) – `False` by default. When `True`, perform one-sided test for excess heterozygosity.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – A struct expression with two fields,
het_freq_hwe ([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")) and p_value
([`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")).

hail.expr.functions.binom_test(_x_ , _n_ , _p_ ,
_alternative_)[[source]](../_modules/hail/expr/functions.html#binom_test)

    

Performs a binomial test on p given x successes in n trials.

Returns the p-value from the [exact binomial
test](https://en.wikipedia.org/wiki/Binomial_test) of the null hypothesis that
success has probability p, given x successes in n trials.

The alternatives are interpreted as follows: \- `'less'`: a one-tailed test of
the significance of x or fewer successes, \- `'greater'`: a one-tailed test of
the significance of x or more successes, and \- `'two-sided'`: a two-tailed
test of the significance of x or any equivalent or more unlikely outcome.

Examples

All the examples below use a fair coin as the null hypothesis. Zero is
interpreted as tail and one as heads.

Test if a coin is biased towards heads or tails after observing two heads out
of ten flips:

    
    
    >>> hl.eval(hl.binom_test(2, 10, 0.5, 'two-sided'))
    0.10937499999999994
    

Test if a coin is biased towards tails after observing four heads out of ten
flips:

    
    
    >>> hl.eval(hl.binom_test(4, 10, 0.5, 'less'))
    0.3769531250000001
    

Test if a coin is biased towards heads after observing thirty-two heads out of
fifty flips:

    
    
    >>> hl.eval(hl.binom_test(32, 50, 0.5, 'greater'))
    0.03245432353613613
    

Parameters:

    

  * **x** (int or ```Expression``` of type ```tint32```) – Number of successes.

  * **n** (int or ```Expression``` of type ```tint32```) – Number of trials.

  * **p** (float or ```Expression``` of type ```tfloat64```) – Probability of success, between 0 and 1.

  * **alternative** – : One of, “two-sided”, “greater”, “less”, (deprecated: “two.sided”).

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64") – p-value.

hail.expr.functions.pchisqtail(_x_ , _df_ , _ncp =None_, _lower_tail =False_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#pchisqtail)

    

Returns the probability under the right-tail starting at x for a chi-squared
distribution with df degrees of freedom.

Examples

    
    
    >>> hl.eval(hl.pchisqtail(5, 1))
    0.025347318677468304
    
    
    
    >>> hl.eval(hl.pchisqtail(5, 1, ncp=2))
    0.20571085634347097
    
    
    
    >>> hl.eval(hl.pchisqtail(5, 1, lower_tail=True))
    0.9746526813225317
    
    
    
    >>> hl.eval(hl.pchisqtail(5, 1, log_p=True))
    -3.6750823266311876
    

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```) – The value at which to evaluate the CDF.

  * **df** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom.

  * **ncp** (float or ```Expression``` of type ```tfloat64```) – Noncentrality parameter, defaults to 0 if unspecified.

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.pgenchisq(_x_ , _w_ , _k_ , _lam_ , _mu_ , _sigma_ , _*_ ,
_max_iterations =None_, _min_accuracy
=None_)[[source]](../_modules/hail/expr/functions.html#pgenchisq)

    

The cumulative probability function of a [generalized chi-squared
distribution](https://en.wikipedia.org/wiki/Generalized_chi-
squared_distribution).

The generalized chi-squared distribution has many interpretations. We share
here four interpretations of the values of this distribution:

  1. A linear combination of normal variables and squares of normal variables.

  2. A weighted sum of sums of squares of normally distributed values plus a normally distributed value.

  3. A weighted sum of chi-squared distributed values plus a normally distributed value.

  4. A [“quadratic form”](https://en.wikipedia.org/wiki/Quadratic_form_\(statistics\)) in a vector of uncorrelated [standard normal](https://en.wikipedia.org/wiki/Normal_distribution#Standard_normal_distribution) values.

The parameters of this function correspond to the parameters of the third
interpretation.

\\[\begin{aligned} w &: R^n \quad k : Z^n \quad lam : R^n \quad mu : R \quad
sigma : R \\\ \\\ x &\sim N(mu, sigma^2) \\\ y_i &\sim
\mathrm{NonCentralChiSquared}(k_i, lam_i) \\\ \\\ Z &= x + w y^T \\\ &= x +
\sum_i w_i y_i \\\ Z &\sim \mathrm{GeneralizedNonCentralChiSquared}(w, k, lam,
mu, sigma) \end{aligned}\\]

The generalized chi-squared distribution often arises when working on linear
models with standard normal noise because the sum of the squares of the
residuals should follow a generalized chi-squared distribution.

Examples

The following plot shows three examples of the generalized chi-squared
cumulative distribution function.

[![Plots of examples of the generalized chi-square cumulative distribution
function. Created by
Dvidby0.](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Generalized_chi-
square_cumulative_distribution_function.svg/1280px-Generalized_chi-
square_cumulative_distribution_function.svg.png)](https://commons.wikimedia.org/wiki/File:Generalized_chi-
square_cumulative_distribution_function.svg)

The following examples are chosen from the three instances shown above. The
curves appear in the same order as the legend of the plot: blue, red, yellow.

    
    
    >>> hl.eval(hl.pgenchisq(-80, w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.0
    >>> hl.eval(hl.pgenchisq(-20, w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.0
    >>> hl.eval(hl.pgenchisq(10 , w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.4670012373599629
    >>> hl.eval(hl.pgenchisq(40 , w=[1, 2], k=[1, 4], lam=[1, 1], mu=0, sigma=0).value)
    0.9958803111156718
    
    
    
    >>> hl.eval(hl.pgenchisq(-80, w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    9.227056966837344e-05
    >>> hl.eval(hl.pgenchisq(-20, w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    0.516439358616939
    >>> hl.eval(hl.pgenchisq(10 , w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    1.0
    >>> hl.eval(hl.pgenchisq(40 , w=[-2, -1], k=[5, 2], lam=[3, 1], mu=-3, sigma=0).value)
    1.0
    
    
    
    >>> hl.eval(hl.pgenchisq(-80, w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.14284718767288906
    >>> hl.eval(hl.pgenchisq(-20, w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.5950150356303258
    >>> hl.eval(hl.pgenchisq(10 , w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.923219534175858
    >>> hl.eval(hl.pgenchisq(40 , w=[1, -10, 2], k=[1, 2, 3], lam=[2, 3, 7], mu=-10, sigma=0).value)
    0.9971746768781656
    

Notes

We follow Wikipedia’s notational conventions. Some texts refer to the weight
vector (our w) as \\(\lambda\\) or lb and the non-centrality vector (our lam)
as nc.

We use the Davies’ algorithm which was published as:

> [Davies, Robert. “The distribution of a linear combination of chi-squared
> random variables.” Applied Statistics 29 323-333.
> 1980.](http://www.robertnz.net/pdf/lc_chisq.pdf)

Davies included Fortran source code in the original publication. Davies also
released a [C language port](http://www.robertnz.net/QF.htm). Hail’s
implementation is a fairly direct port of the C implementation to Scala.
Davies provides 39 test cases with the source code. The Hail tests include all
39 test cases as well as a few additional tests.

Davies’ website cautions:

> The method works well in most situations if you want only modest accuracy,
> say 0.0001. But problems may arise if the sum is dominated by one or two
> terms with a total of only one or two degrees of freedom and x is small.

For an accessible introduction the Generalized Chi-Squared Distribution, we
strongly recommend the introduction of this paper:

> [Das, Abhranil; Geisler, Wilson (2020). “A method to integrate and classify
> normal distributions”.](https://arxiv.org/abs/2012.14331)

Parameters:

    

  * **x** (```float``` or ```Expression``` of type ```tfloat64```) – The value at which to evaluate the cumulative distribution function (CDF).

  * **w** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```float``` or ```Expression``` of type ```tarray``` of ```tfloat64```) – A weight for each non-central chi-square term.

  * **k** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```int``` or ```Expression``` of type ```tarray``` of ```tint32```) – A degrees of freedom parameter for each non-central chi-square term.

  * **lam** ([`list`](https://docs.python.org/3.9/library/stdtypes.html#list "\(in Python v3.9\)") of ```float``` or ```Expression``` of type ```tarray``` of ```tfloat64```) – A non-centrality parameter for each non-central chi-square term. We use lam instead of lambda because the latter is a reserved word in Python.

  * **mu** (```float``` or ```Expression``` of type ```tfloat64```) – The standard deviation of the normal term.

  * **sigma** (```float``` or ```Expression``` of type ```tfloat64```) – The standard deviation of the normal term.

  * **max_iterations** (```int``` or ```Expression``` of type ```tint32```) – The maximum number of iterations of the numerical integration before raising an error. The default maximum number of iterations is `1e5`.

  * **min_accuracy** (```int``` or ```Expression``` of type ```tint32```) – The minimum accuracy of the returned value. If the minimum accuracy is not achieved, this function will raise an error. The default minimum accuracy is `1e-5`.

Returns:

    

[`StructExpression`](../hail.expr.StructExpression.html#hail.expr.StructExpression
"hail.expr.StructExpression") – This method returns a structure with the value
as well as information about the numerical integration.

  * value : ```Float64Expression```. If converged is true, the value of the CDF evaluated at x. Otherwise, this is the last value the integration evaluated before aborting.

  * n_iterations : ```Int32Expression```. The number of iterations before stopping.

  * converged : ```BooleanExpression```. True if the min_accuracy was achieved and round off error is not likely significant.

  * fault : ```Int32Expression```. If converged is true, fault is zero. If converged is false, fault is either one or two. One indicates that the requried accuracy was not achieved. Two indicates the round-off error is possibly significant.

hail.expr.functions.pnorm(_x_ , _mu =0_, _sigma =1_, _lower_tail =True_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#pnorm)

    

The cumulative probability function of a normal distribution with mean mu and
standard deviation sigma. Returns cumulative probability of standard normal
distribution by default.

Examples

    
    
    >>> hl.eval(hl.pnorm(0))
    0.5
    
    
    
    >>> hl.eval(hl.pnorm(1, mu=2, sigma=2))
    0.30853753872598694
    
    
    
    >>> hl.eval(hl.pnorm(2, lower_tail=False))
    0.022750131948179212
    
    
    
    >>> hl.eval(hl.pnorm(2, log_p=True))
    -0.023012909328963493
    

Notes

Returns the left-tail probability p = Prob(\\(Z < x\\)) with \\(Z\\) a normal
random variable. Defaults to a standard normal random variable.

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **mu** (float or ```Expression``` of type ```tfloat64```) – Mean (default = 0).

  * **sigma** (float or ```Expression``` of type ```tfloat64```) – Standard deviation (default = 1).

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.pT(_x_ , _n_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#pT)

    

The cumulative probability function of a
[t-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution)
with n degrees of freedom.

Examples

    
    
    >>> hl.eval(hl.pT(0, 10))
    0.5
    
    
    
    >>> hl.eval(hl.pT(1, 10))
    0.82955343384897
    
    
    
    >>> hl.eval(hl.pT(1, 10, lower_tail=False))
    0.17044656615103004
    
    
    
    >>> hl.eval(hl.pT(1, 10, log_p=True))
    -0.186867754489647
    

Notes

If lower_tail is true, returns Prob(\\(X \leq\\) x) where \\(X\\) is a
t-distributed random variable with n degrees of freedom. If lower_tail is
false, returns Prob(\\(X\\) > x).

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **n** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom of the t-distribution.

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.pF(_x_ , _df1_ , _df2_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#pF)

    

The cumulative probability function of a
[F-distribution](https://en.wikipedia.org/wiki/F-distribution) with parameters
df1 and df2.

Examples

    
    
    >>> hl.eval(hl.pF(0, 3, 10))
    0.0
    
    
    
    >>> hl.eval(hl.pF(1, 3, 10))
    0.5676627969783028
    
    
    
    >>> hl.eval(hl.pF(1, 3, 10, lower_tail=False))
    0.4323372030216972
    
    
    
    >>> hl.eval(hl.pF(1, 3, 10, log_p=True))
    -0.566227703842908
    

Notes

If lower_tail is true, returns Prob(\\(X \leq\\) x) where \\(X\\) is a random
variable with distribution \\(F`(df1, df2). If `lower_tail\\) is false,
returns Prob(\\(X\\) > x).

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **df1** (float or ```Expression``` of type ```tfloat64```) – Parameter of the F-distribution

  * **df2** (float or ```Expression``` of type ```tfloat64```) – Parameter of the F-distribution

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.ppois(_x_ , _lamb_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#ppois)

    

The cumulative probability function of a Poisson distribution.

Examples

    
    
    >>> hl.eval(hl.ppois(2, 1))
    0.9196986029286058
    

Notes

If lower_tail is true, returns Prob(\\(X \leq\\) x) where \\(X\\) is a Poisson
random variable with rate parameter lamb. If lower_tail is false, returns
Prob(\\(X\\) > x).

Parameters:

    

  * **x** (float or ```Expression``` of type ```tfloat64```)

  * **lamb** (float or ```Expression``` of type ```tfloat64```) – Rate parameter of Poisson distribution.

  * **lower_tail** (bool or ```BooleanExpression```) – If `True`, compute the probability of an outcome at or below x, otherwise greater than x.

  * **log_p** (bool or ```BooleanExpression```) – Return the natural logarithm of the probability.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.qchisqtail(_p_ , _df_ , _ncp =None_, _lower_tail =False_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#qchisqtail)

    

The quantile function of a chi-squared distribution with df degrees of
freedom, inverts `pchisqtail()`.

Examples

    
    
    >>> hl.eval(hl.qchisqtail(0.05, 2))
    5.991464547107979
    
    
    
    >>> hl.eval(hl.qchisqtail(0.05, 2, ncp=2))
    10.838131614372958
    
    
    
    >>> hl.eval(hl.qchisqtail(0.05, 2, lower_tail=True))
    0.10258658877510107
    
    
    
    >>> hl.eval(hl.qchisqtail(hl.log(0.05), 2, log_p=True))
    5.991464547107979
    

Notes

Returns right-quantile x for which p = Prob(\\(Z^2\\) > x) with \\(Z^2\\) a
chi-squared random variable with degrees of freedom specified by df. The
probability p must satisfy 0 < p < 1.

Parameters:

    

  * **p** (float or ```Expression``` of type ```tfloat64```) – Probability.

  * **df** (float or ```Expression``` of type ```tfloat64```) – Degrees of freedom.

  * **ncp** (float or ```Expression``` of type ```tfloat64```) – Corresponds to ncp parameter in `pchisqtail()`.

  * **lower_tail** (bool or ```BooleanExpression```) – Corresponds to lower_tail parameter in `pchisqtail()`.

  * **log_p** (bool or ```BooleanExpression```) – Exponentiate p, corresponds to log_p parameter in `pchisqtail()`.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.qnorm(_p_ , _mu =0_, _sigma =1_, _lower_tail =True_,
_log_p =False_)[[source]](../_modules/hail/expr/functions.html#qnorm)

    

The quantile function of a normal distribution with mean mu and standard
deviation sigma, inverts `pnorm()`. Returns quantile of standard normal
distribution by default.

Examples

    
    
    >>> hl.eval(hl.qnorm(0.90))
    1.2815515655446008
    
    
    
    >>> hl.eval(hl.qnorm(0.90, mu=1, sigma=2))
    3.5631031310892016
    
    
    
    >>> hl.eval(hl.qnorm(0.90, lower_tail=False))
    -1.2815515655446008
    
    
    
    >>> hl.eval(hl.qnorm(hl.log(0.90), log_p=True))
    1.2815515655446008
    

Notes

Returns left-quantile x for which p = Prob(\\(Z\\) < x) with \\(Z\\) a normal
random variable with mean mu and standard deviation sigma. Defaults to a
standard normal random variable, and the probability p must satisfy 0 < p < 1.

Parameters:

    

  * **p** (float or ```Expression``` of type ```tfloat64```) – Probability.

  * **mu** (float or ```Expression``` of type ```tfloat64```) – Mean (default = 0).

  * **sigma** (float or ```Expression``` of type ```tfloat64```) – Standard deviation (default = 1).

  * **lower_tail** (bool or ```BooleanExpression```) – Corresponds to lower_tail parameter in `pnorm()`.

  * **log_p** (bool or ```BooleanExpression```) – Exponentiate p, corresponds to log_p parameter in `pnorm()`.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

hail.expr.functions.qpois(_p_ , _lamb_ , _lower_tail =True_, _log_p
=False_)[[source]](../_modules/hail/expr/functions.html#qpois)

    

The quantile function of a Poisson distribution with rate parameter lamb,
inverts `ppois()`.

Examples

    
    
    >>> hl.eval(hl.qpois(0.99, 1))
    4
    

Notes

Returns the smallest integer \\(x\\) such that Prob(\\(X \leq x\\)) \\(\geq\\)
p where \\(X\\) is a Poisson random variable with rate parameter lambda.

Parameters:

    

  * **p** (float or ```Expression``` of type ```tfloat64```)

  * **lamb** (float or ```Expression``` of type ```tfloat64```) – Rate parameter of Poisson distribution.

  * **lower_tail** (bool or ```BooleanExpression```) – Corresponds to lower_tail parameter in inverse `ppois()`.

  * **log_p** (bool or ```BooleanExpression```) – Exponentiate p before testing.

Returns:

    

[`Expression`](../hail.expr.Expression.html#hail.expr.Expression
"hail.expr.Expression") of type
[`tfloat64`](../types.html#hail.expr.types.tfloat64
"hail.expr.types.tfloat64")

---

# Trio

_class
_hail.genetics.Trio[[source]](../_modules/hail/genetics/pedigree.html#Trio)

    

Bases: [`object`](https://docs.python.org/3.9/library/functions.html#object
"\(in Python v3.9\)")

Class containing information about nuclear family relatedness and sex.

Parameters:

    

  * **s** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)")) – Sample ID of proband.

  * **fam_id** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") _or_ _None_) – Family ID.

  * **pat_id** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") _or_ _None_) – Sample ID of father.

  * **mat_id** ([_str_](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python v3.9\)") _or_ _None_) – Sample ID of mother.

  * **is_female** ([_bool_](https://docs.python.org/3.9/library/functions.html#bool "\(in Python v3.9\)") _or_ _None_) – Sex of proband.

Attributes

`fam_id` | Family ID.  
---|---  
`is_female` | Returns `True` if the proband is a reported female, `False` if reported male, and `None` if no sex is defined.  
`is_male` | Returns `True` if the proband is a reported male, `False` if reported female, and `None` if no sex is defined.  
`mat_id` | ID of mother in trio, may be missing.  
`pat_id` | ID of father in trio, may be missing.  
`s` | ID of proband in trio, never missing.  
  
Methods

`is_complete` | Returns True if the trio has a defined mother and father.  
---|---  
  
_property _fam_id

    

Family ID.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or None

is_complete()[[source]](../_modules/hail/genetics/pedigree.html#Trio.is_complete)

    

Returns True if the trio has a defined mother and father.

The considered fields are `mat_id()` and `pat_id()`. Recall that `s` may never
be missing. The `fam_id()` and `is_female()` fields may be missing in a
complete trio.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)")

_property _is_female

    

Returns `True` if the proband is a reported female, `False` if reported male,
and `None` if no sex is defined.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)") or None

_property _is_male

    

Returns `True` if the proband is a reported male, `False` if reported female,
and `None` if no sex is defined.

Return type:

    

[bool](https://docs.python.org/3.9/library/functions.html#bool "\(in Python
v3.9\)") or None

_property _mat_id

    

ID of mother in trio, may be missing.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or None

_property _pat_id

    

ID of father in trio, may be missing.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)") or None

_property _s

    

ID of proband in trio, never missing.

Return type:

    

[str](https://docs.python.org/3.9/library/stdtypes.html#str "\(in Python
v3.9\)")