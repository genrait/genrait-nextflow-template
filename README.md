![The GenRAIT logo](./assets/genrait.png)
# Nextflow Template for the genRAITplatform

This template provides a basic starting point for working with [Nextflow](https://www.nextflow.io/) and [nf-core](https://nf-co.re/) on the [GenRAIT](https://genrait.com/) platform.  It is based on [nf-core/demo](https://github.com/nf-core/demo).

## Usage

> [!NOTE]
> If you are new to Nextflow and nf-core, please refer to [this page](https://nf-co.re/docs/usage/installation) on how to set-up Nextflow. Make sure to [test your setup](https://nf-co.re/docs/usage/introduction#how-to-run-a-pipeline) with `-profile test` before running the workflow on actual data.

### Requirements
In order to install modules and update a workflows parameters the user needs:

- [nf-core/tools](https://nf-co.re/docs/nf-core-tools/installation)
- [Nextflow](https://www.nextflow.io/)

### Using nf-core modules

We recommend using the nf-core ecosystem where possible, especially using nf-core [modules](https://nf-co.re/modules).


List available modules:

``` bash
nf-core modules list remote
```

Install a module:

``` bash
nf-core modules install zip
```

Modules will be placed in the `modules` directory, and can be imported like any other Nextflow module:

``` groovy
include { FASTQC } from '../modules/nf-core/fastqc/main'

workflow EXAMPLE_WORKFLOW {

    take:
    ch_samplesheet 
    main:

    ch_versions = Channel.empty()
    ch_multiqc_files = Channel.empty()
    //
    // MODULE: Run FastQC
    //
    FASTQC (
        ch_samplesheet
    )
    FASTQC.out.view()
}
```


### Writing processes

The user can refer to the Nextflow [documentation](https://www.nextflow.io/docs/latest/process.html) on writing processes.  In order for the genRAIT platform to resolve software dependencies, the user needs to declare per-process or global dependencies with the [conda directive](https://www.nextflow.io/docs/latest/reference/process.html#conda).

``` groovy
process bwaIndex {
    conda "bwa=0.7.15" // <---

    input:
    path reference

    output:
    tuple val(reference.name), path("${reference}.amb"), path("${reference}.ann"), path("${reference}.bwt"), path("${reference}.sa"), path("${reference}.pac")

    """
    bwa index $reference
    """
}
```

### Parameters

In order for the genRAIT platform to import a workflow, there must be a `nextflow_schema.json` in the root directory.  Pipeline [parameters](https://www.nextflow.io/docs/latest/config.html#parameters) must be declared in `nextflow.config` (see [here](https://www.nextflow.io/docs/latest/workflow.html#using-parameters) for more information on how to use parameters in your pipeline).

When new parameters are added, use the nf-core schema builder to rebuild the file:

``` bash
nf-core pipelines schema build
```


> [!NOTE]
> Parameters which represent files or folders during runtime must be declared as such, either through the interactive schema builder, or manually in the `nextflow_schema.json`.
>
> ![The GenRAIT logo](./assets/genrait.png) 
>
> Here is a parameter marked as a folder in the schema file:
> ``` json
> ...
>   "properties": {
>     "outputDir": {
>       "type": "string",
>       "default": "./",
>       "fa_icon": "fas fa-folder",
>       "format": "directory-path",
>       "hidden": false
>     }
>   }
> ...
> ```


### Importing your workflow on genRAIT

Import your folder to your genRAIT storage and select "Create Workflow" from the dropdown.

<!-- TODO Add screenshot here -->

## Citations

<!-- TODO nf-core: Add citation for pipeline after first release. Uncomment lines below and update Zenodo doi and badge at the top of this file. -->
<!-- If you use genrait/nextflow-template for your analysis, please cite it using the following doi: [10.5281/zenodo.XXXXXX](https://doi.org/10.5281/zenodo.XXXXXX) -->

<!-- TODO nf-core: Add bibliography of tools and data used in your pipeline -->

An extensive list of references for the tools used by the pipeline can be found in the [`CITATIONS.md`](CITATIONS.md) file.

This pipeline uses code and infrastructure developed and maintained by the [nf-core](https://nf-co.re) community, reused here under the [MIT license](https://github.com/nf-core/tools/blob/main/LICENSE).

> **The nf-core framework for community-curated bioinformatics pipelines.**
>
> Philip Ewels, Alexander Peltzer, Sven Fillinger, Harshil Patel, Johannes Alneberg, Andreas Wilm, Maxime Ulysse Garcia, Paolo Di Tommaso & Sven Nahnsen.
>
> _Nat Biotechnol._ 2020 Feb 13. doi: [10.1038/s41587-020-0439-x](https://dx.doi.org/10.1038/s41587-020-0439-x).
