# Sunbeam extension template

This is a template to use to extend the [Sunbeam pipeline](https://github.com/sunbeam-labs/sunbeam). There are three major parts to a Sunbeam extension: 

 - `sbx_template_env.yml` specifies the extension's dependencies
 - `config.yml` contains configuration options that can be specified by the user when running an extension
 - `sbx_template.rules` contains the rules (logic/commands run) of the extension
 
## Creating an extension

Any dependencies (available through conda) required for an extension should be listed in the `sbx_[your_extension_name]_env.yml` file. An example of how to format this is shown in the `sbx_template_env.yml` file. These dependencies are automatically handled and installed by Snakemake through conda (see below for how to make sure your rule finds this env file), so the user doesn't have to worry about installing dependencies themselves. You can also specify specific software versions like so:

    dependencies:
      - python=3.7
      - megahit<2

Rarely, you may want to specify different environments for different rules within your extension, in the event that different rules have different (potentially conflicting) requirements.

The `config.yml` contains parameters that the user might need to modify when running an extension. For example, if your downstream analysis is run differently depending on whether reads are paired- or single-end, it would probably be wise to include a `paired_end` parameter. Default values should be specified for each terminal key. As of Sunbeam v3.0, as long as the parameter config file is named `config.yml`, configuration options for installed extensions are automatically included in new config files generated by `sunbeam init` and `sunbeam config update`.

Finally, `sbx_template.rules` contains the actual logic for the extension, including required input and output files. A detailed discussion of Snakemake rule creation is beyond the scope of this tutorial, but definitely check out [the Snakemake tutorial](http://snakemake.readthedocs.io/en/stable/tutorial/basics.html) and any of the [extensions by sunbeam-labs](https://github.com/sunbeam-labs) for inspiration.

For each rule that needs dependencies from your environment file, make sure to let snakemake know in the rule like this:

    example_rule:
        ...
        conda:
            "sbx_template_env.yml"
        ...

The dependency .yml file can be named whatever you want, as long as you refer to it by the correct filename in whatever rule needs those dependencies. The path to the dependency .yml file is relative to the .rules file (which in most cases is in the same directory).

## Installing an extension

Extension install is as simple as passing the extension's URL on GitHub to `sunbeam extend`:

    sunbeam extend https://github.com/sunbeam-labs/sbx_template

Any user-modifiable parameters specified in `config.yml` are automatically added on `sunbeam init`. If you're installing an extension in a project where you already have a config file, run the following to add the options for your newly added extension to your config (the `-i` flag means in-place config file modification; remove the `-i` flag to see the new config in stdout):

    sunbeam config update -i sunbeam_config.yml

Installation instructions for older versions of Sunbeam are included at the end of this README.

## Running an extension

To run an extension, simply run Sunbeam as usual with your extension's target rule specified:

    sunbeam run --configfile=sunbeam_config.yml --use-conda example_rule

The `--use-conda` flag is required to let Snakemake know that you want to use the conda environment(s) included with your extension.

-------
    
## Installing an extension (legacy instructions for sunbeam <3.0)

Installing an extension is as simple as cloning (or moving) your extension directory into the sunbeam/extensions/ folder, installing requirements through Conda, and adding the new options to your existing configuration file: 

    git clone https://github.com/louiejtaylor/sbx_template/ sunbeam/extensions/sbx_template
    cat sunbeam/extensions/sbx_template/config.yml >> sunbeam_config.yml
