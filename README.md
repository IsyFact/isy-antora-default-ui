# IsyFact Antora Default UI

This project is a central repository providing a customized Antora Default UI for all IsyFact projects.
Based on the [Antora Default UI project](https://gitlab.com/antora/antora-ui-default) (master, commit 0e38223) it provides source files and tools for building the UI-Bundle distributed as `ui-bundle.zip` which is used by Antora projects for rendering HTML-pages during the documentation generation process.

> **NOTE:** This project **must not** contain any confidential or project specific files like logo, styles etc. (branding).

Main purpose of this project is:
- **Single Source of Truth** for the UI-Bundle, which can be maintained and tracked at one place
- **Standardized distribution and integration** of UI-Bundle changes into Antora Documentation projects
- **Solution working also for security sensitive environments**, where network access to publicly available repositories is strictly limited

## Prerequisites

In order to build and preview the UI-Bundle following tools from the Node.js family are needed:

- [Node.js](https://nodejs.org/en) (`npm` and `node`)
- [NVM](https://www.nvmnode.com/) (highly recommended for managing Node versions)

> **NOTE:** The build process relies on `gulp`.
> We recommend using `npx` instead of a global `gulp` installation to avoid version conflicts.

## Project Setup

1. Install project dependencies by: `npm install`
2. Verify installation running `gulp` from local installation, e.g:
   ```shell
   $ npx gulp --version
   CLI version: 2.3.0
   Local version: 4.0.2
   ```
3. Install `gulp` globally (optional, for convenience only):
   ```shell
   npm install -g gulp
   gulp --version
   ```
## Building the Default UI and UI-Bundle

After successful [project setup](#project-setup) you can build and bundle the Default UI in one go with:

`npx gulp bundle`

* After this command the Default UI is rendered into the folder `public`
* The Default UI is bundled into `build/ui-bundle.zip`

## Integrate IsyFact UI-Bundle into Antora Project

The UI-Bundle can be integrated into your Antora project using `git subtree`.
Assuming `antora-ui/default-ui` as target directory and `https://github.com/IsyFact/isy-antora-default-ui.git` is the source Repository, following steps are required to achieve this.

1. **Add the IsyFact Antora Default UI as a subtree to your Antora Project:**
   ```shell
   git subtree add --prefix antora-ui/default-ui https://github.com/IsyFact/isy-antora-default-ui.git v1 --squash
   ```
   this step will do two things:
   * copy all the files of the Default UI project into `antora-ui/default-ui` at git release tag `v1`
   * add two commits to your history (merge commit + squash commit)
   * the option `--squash` prevents pollution of the Git history

   If you want to keep all files for some reason (full control over source files, verify build) you can push your branch and you are finished.
   The UI-Bundle is saved at `antora-ui/default-ui/build/ui-bundle.zip` and can be referenced by your Antora playbook file using `./antora-ui/default-ui/build/ui-bundle.zip` as url.
   Otherwise, continue with step 2.

2. **Add only `ui-bundle.zip` to keep your project's Git history clean and avoid importing the entire UI source code history:**
   ```shell
   git reset --soft <hash-of-your-last-clean-commit>
   git restore --staged antora-ui/default-ui
   git add antora-ui/default-ui/build/ui-bundle.zip
   # update ui-bundle url in antora-playbook.yml and git add antora-playbook.yml
   git commit -m "chore: use IsyFact ui-bundle (version v1) from central repo"
   ```
   After this step you should have only one commit `chore: use IsyFact ui-bundle (version v1) from central repo` copying only the file `build/ui-bundle.zip` from the Default UI project.
   If the documentation build is fine you can push to your branch.

3. **Cleanup remaining files in `antora-ui/default-ui`:**
   ```shell
   git clean -fd antora-ui/default-ui
   ```

> **NOTE on Git**: This integration process leverages standard Git workflows to ensure platform independence and reliability.

## Update IsyFact Antora Default UI / UI-Bundle

If you are already using the IsyFact Default UI and want to update to a newer version, the steps are similar to [Integrate IsyFact UI-Bundle into Antora Project](#Integrate-IsyFact-UI-Bundle-into-Antora-Project).

1. **Delete existing target directory:**
   ```shell
   git rm -rf antora-ui/default-ui
   git commit -m "chore: delete ui-bundle.zip for update"
   ```
   Because `git subtree` does not allow the target directory exist, `antora-ui/default-ui` has to be deleted.
   The commit for deletion of the UI-Bundle is needed, because `git subtree` requires a clean working tree.

2. **Re-add the Default UI / UI-Bundle according to [Integrate IsyFact UI-Bundle into Antora Project](#Integrate-IsyFact-UI-Bundle-into-Antora-Project):**
   ```shell
   git subtree add --prefix antora-ui/default-ui https://github.com/IsyFact/isy-antora-default-ui.git v2 --squash
   ```

## Release

1. **Change the source files and rebuild the Default UI:**
   ```shell
   npx gulp bundle
   ```
2. **Publish changes:**
   ```shell
   git add .
   git commit -m "changes for release v3"
   git tag -a v3 -m "release v3"
   git push origin main
   git push v3
   ```
> **NOTE on Versioning:** New releases are tagged by v1, v2... on the main branch to keep it simple.

## Resources

* [Antora Default UI project](https://gitlab.com/antora/antora-ui-default)
* [Antora Default UI Documentation](https://docs.antora.org/antora-ui-default/)
* [Build Tool Gulp](https://gulpjs.com/)

## Copyright and License

The use of this software is granted under the terms of the [Mozilla Public License Version 2.0](https://www.mozilla.org/en-US/MPL/2.0/) (MPL-2.0).
See [LICENCE](./LICENSE) to find the full license text.
