# FTL: Multiverse Translation Project

## Running the script

The script is written in Python and managed by poetry. A standard setuptools may install it as a package,
or you may use poetry to manage the virtualenv and installation process.

The `mvloc` command can be invoked with following setups:

1. Use pip to install the package. (virtualenv is recommended)
2. Use poetry to install the package (`poetry install`) then run it by `poetry run mvloc`.

All `mvloc` subcommands starting with `batch-` generates `report.txt` file that contains logs and outputs of all task
in a workflow.

## Workflows

### Translation

The repo is designed to work with [Weblate](https://weblate.org/). Following Weblate addon settings are recommended:

* Component discovery
   ```
   Regular expression to match translation files against:
   locale/(?P<component>.*)/(?P<language>[^/.]*)\.po
   
   File format:
   gettext PO file (monolingual)
   
   Define the monolingual base filename:
   locale/{{ component }}/en.po
   
   Define the base file for new translations:
   locale/{{ component }}/en.po
   ```

### Updating the English strings

1. Unzip the latest FTL: Multiverse into src-en/ directory
2. Run `mvloc batch-generate --clean en`

The command extracts localizable strings from `src-en/` and updates  `en.po` files in `locale/`. Weblate can
automatically grab the changes once the repository is updated.

### Changing string extraction criteria

Edit `mvloc.config.jsonc` file, then and follow the "Updating the English strings" workflow.

### Applying the translation

1. Unzip the latest FTL: Multiverse into `src-en/` directory
2. Run `mvloc batch-apply <langname>` -- Example: `mvloc batch-apply ko`

The command transforms XMLs in `src-en/` using translation files on `locale/`,
then writes them out to `output/<langname>` directory.

### Bootstrapping

1. Unzip the original FTL: Multiverse into `src-en/` directory
2. Create `src-<langname>/` directory and place the translated XMLs there -- Example: `src-ko/`.
3. Run `mvloc batch-generate --diff --clean --empty-identical <langname>`

The bootstrapping process tries to reverse the applying process: extracting the strings out of already translated
XML files. This is useful when migrating from an ongoing translation project.

In case where the string extraction criteria is incomplete to cover your XMLs, the unhandled changes are shown in
the report.txt (shown as "Diff report" tasks). In that case, adjust `mvloc.config.jsonc` appropriately, follow the
"Updating the English strings" workflow to update en.po files, and repeat this workflow again.

> #### Bootstrapping only a subset of XML files
>
> * This is useful when importing a new translated XML file over an already existing locale in the project.
> * Put XML files into `src-<langname>/`. Make sure to remove any other files or they will be overwritten as well!
> * Remove `--clean` option when invoking `mvloc`. Other files will be untouched.

## Disclaimer

FTL: Faster Than Light is a trademark of Subset Games. Unless otherwise stated, the authors and the contributors of this
repository is not affiliated with nor endorsed by Subset Games.
