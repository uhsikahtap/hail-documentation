# For Software Developers


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
    

