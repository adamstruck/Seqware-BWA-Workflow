[![Build Status](https://travis-ci.org/ICGC-TCGA-PanCancer/Seqware-BWA-Workflow.svg?branch=develop)](https://travis-ci.org/ICGC-TCGA-PanCancer/Seqware-BWA-Workflow)
[![Docker Repository on Quay](https://quay.io/repository/pancancer/pcawg-bwa-mem-workflow/status "Docker Repository on Quay")](https://quay.io/repository/pancancer/pcawg-bwa-mem-workflow)

# PCAWG BWA-Mem Workflow

## Overview

This is the SeqWare workflow for the TCGA/ICGC PanCancer project that aligns
whole genome sequences with BWA-Mem.  It also reads/writes to GNOS (optionally), the metadata/data
repository system used in the project.  It can now be used in a standalone mode
using the [Dockstore](http://dockstore.org) CLI.
For more information about the workflow see the [CHANGELOG](CHANGELOG.md).

For more information about the project overall see the
[PanCancer wiki space](https://wiki.oicr.on.ca/display/PANCANCER/PANCANCER+Home).

## For Users

### Running via Dockstore

See the Dockstore [page](https://www.dockstore.org/containers/quay.io/pancancer/pcawg-bwa-mem-workflow) for this
 workflow.  The idea is Dockstore links together the Dockstore.cwl descriptor (which tells you how to launch this workflow)
 and the Docker image for this workflow (which contains the compiled workflow).  You can then use the `dockstore` command
 line to easily find parameters, parameterize, and then run the workflow. For this workflow, the Docker image is hosted
 and built on [quay.io](https://quay.io/repository/pancancer/pcawg-bwa-mem-workflow) and the [Dockstore.cwl](Dockstore.cwl)
 is in GitHub.  We also provide a sample [Dockstore.json](Dockstore.json) which includes links for sample reference files
 and input but *you need to update the output to write to an S3 bucket where you have access or a full, local file path*.

    dockstore launch --entry quay.io/pancancer/pcawg-bwa-mem-workflow --json Dockstore.json

This downloads the sample inputs and reference files from HTTP URLs to a local `datastore` working directory, runs the
workflow, and uploads the results back to the location you specified in S3 (or local file path).  You can of course
make a JSON that parameterizes everything with full, local file paths which will skip the file provisioning.

## For Developers

### Building the Workflow

This workflow (like other SeqWare workflows) is built using Maven.  In the
workflow directory (workflow-bwa-pancancer), execute the following:

    mvn clean install

This will take a long time on first build since it download dependencies from Maven
and the reference genome which is 5GB+ in size.

### Building the Worklfow Docker Image

You can also build a Docker image that has the workflow ready to run in it.

    docker build --no-cache -t pancancer/pcawg-bwa-mem-workflow:2.6.8 .

### Testing Locally via Docker

You can run the image and then run the workflow

    docker run -ti --rm pancancer/pcawg-bwa-mem-workflow:2.6.8 /bin/bash
    seqware bundle launch --dir target/Workflow_Bundle_BWA_2.6.8_SeqWare_1.1.1/ --engine whitestar --no-metadata

To save time, if you have reference data handy, you can mount it into the container to save time. You can grab it from the links directory when running the test execution of the workflow like above.  

    docker run -ti --rm -v /<your custom location>/links.bak:/home/seqware/Seqware-BWA-Workflow/links  pancancer/pcawg-bwa-mem-workflow:2.6.8 /bin/bash


## Installation & Running

This is beyond the scope of this README.  However, we recommend using the Docker-based workflow
and run it using the Dockstore command line.

## Sample Data

Some synthetic sample data.

* https://s3.amazonaws.com/oicr.workflow.bundles/released-bundles/synthetic_bam_for_GNOS_upload/hg19.chr22.5x.normal2.bam
* https://s3.amazonaws.com/oicr.workflow.bundles/released-bundles/synthetic_bam_for_GNOS_upload/hg19.chr22.5x.normal.bam

## Reference Data

We use a specific reference based on GRCh37.

* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz
* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz.fai
* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz.64.amb
* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz.64.ann
* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz.64.bwt
* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz.64.pac
* http://s3.amazonaws.com/pan-cancer-data/pan-cancer-reference/genome.fa.gz.64.sa

## Authors

* Brian O'Connor <boconnor@oicr.on.ca>
* Junjun Zhang <Junjun.Zhang@oicr.on.ca>
* Adam Wright <adam.wright@oicr.on.ca>

## Contributors

* Keiran Raine: PCAP-Core and BWA-Mem workflow design
* Roshaan Tahir: Original BWA-Align workflow design

## Workflow Authors' Release Checklist

Make sure you:

* update the workflow version in:
    * workflow.properties
    * pom.xml
    * gnos\_upload\_data.pl
    * make sure you grep for any other files
* update the description of the workflow in workflow.properties and gnos\_upload\_data.pl, this includes differences with the previous release. Update the CHANGELOG which should contain the bulk of documentation about changes and links to our Bug system.
* test the workflow locally (VM) and at clouds
* do not package your gnostest.pem key!
* release in Github using HubFlow
* upload the workflow zip to S3
