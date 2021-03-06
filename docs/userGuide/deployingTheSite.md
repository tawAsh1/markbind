<variable name="title" id="title">Deploying the Site</variable>
<frontmatter>
  title: "User Guide: {{ title }}"
  footer: footer.md
  siteNav: userGuideSections.md
  pageNav: default
</frontmatter>

<include src="../common/header.md" />

# {{ title }}

<span class="lead">

**A site generated by MarkBind can be deployed by simply uploading the generated files to any Web server.** In addition, MarkBind provides several convenient deployment options.
</span>

Generic steps for deploying a MarkBind site:
1. Set the [`baseUrl` property of the `site.json` file]({{ baseUrl }}/userGuide/siteConfiguration.html#baseUrl) to match the deploy location.
1. (Optional) Use the [`markbind serve` command]({{ baseUrl }}/userGuide/cliCommands.html#serve-command) to stage the site locally and confirm the contents are as expected.
1. Use the [`markbind build` command]({{ baseUrl }}/userGuide/cliCommands.html#build-command) to generate the site from source files. That command puts the generated site files in a directory named `_site` (you can change the output directory using parameters supplied to the command).
1. Upload the site files to the Web server. The sections below explains how to automate this step for when deploying to some online platforms.

## Deploying to Github Pages

**MarkBind can easily deploy a site to [Github pages](https://help.github.com/categories/github-pages-basics/)** if the project root directory is also a GitHub repo.

Running the **`markbind deploy`** command will deploy the most recent build of your site to the `gh-pages` branch of the repo `origin` and will be available
After the command is completed, your site will be online at `http://<username|org>.github.io/<repo>` e.g., http://se-edu.github.io/se-book.

<div id="warning-about-baseUrl">

<box type="warning">

If you are deploying to the site to GitHub pages, the `baseUrl` setting in the `site.json` should be set to the `"/<repositoryName>"` for the links in the deployed site to work correctly.<br>
{{ icon_example }} If you are using Github Pages to host your deployed website at repo `myorg/myproduct` (i.e., the website is published at `https://myorg.github.io/myproduct`), then your `baseUrl` should be `"/myproduct"`.
</box>
</div>

You can override the default deployment settings %%(e.g., repo/branch to deploy)%% in the `site.json`'s `deploy` section:

<panel type="seamless" header="**User Guide: Configuring the Site → `deploy`**" popup-url="{{ baseUrl }}/userGuide/siteConfiguration.html#deploy">
  <include src="siteConfiguration.md#site-json-deploy" />
</panel>

<br>
<box type="warning">

`markbind deploy` does not generate the static site from your source; it simply deploys the files that are already in the `_site` directory. You need to run `markbind build` first if you want to generate the site before deploying.
</box>

### Deploying to GitHub Pages via Travis CI
**You can setup [<tooltip content="a platform for Continuous Integration and Delivery (among other things)">Travis CI</tooltip>](https://www.travis-ci.org/) to automatically build and deploy your site on GitHub Pages every time your GitHub repo is updated.**


#### Adding your repository to Travis CI

<panel header="{{icon_info}} Travis CI Migration" type="info" expanded>

Since May 2018, Travis CI has been [undergoing migration to `travis-ci.com`](https://docs.travis-ci.com/user/migrate/open-source-on-travis-ci-com/), which changes the way you setup repositories on Travis CI. 

- If you are new to Travis CI, you should **add your repository on `travis-ci.com`**.
- Alternatively, if you are already using Travis CI on `travis-ci.org`, you can continue to **add your repository on `travis-ci.org`**.
</panel>
<p/>
<tabs>
  <tab header="Add your repository on `travis-ci.com`">

1. [Sign in to Travis](https://travis-ci.com/signin) using your GitHub account.
1. Accept the authorisation for Travis CI when you are redirected to GitHub.
1. Go to the [Repositories page](https://travis-ci.com/account/repositories), and click on the green _Activate_ button.<br>
  %%{{ icon_info }} If you are already using Travis CI, click on the white _Manage Repositories on GitHub_ button instead.%%
1. Select the repository with the MarkBind site, and add the Travis CI GitHub App to the repository by clicking the green _Approve and Install_ button.

  </tab>
  <tab header="Add your repository on `travis-ci.org`">

1. [Sign in to Travis](https://travis-ci.com/signin) using your GitHub account.
1. Accept the authorisation for Travis CI when you are redirected to GitHub.
1. Go to the [Repositories page](https://travis-ci.org/account/repositories).
1. Find the repository with the MarkBind site.<br>
  %%{{ icon_info }} If the organization/repository is not shown in the list, click on `Review and add` link at the bottom of the list of organization and follow the steps to give Travis access to the organization containing your repo. After that, come back to the [Repositories page](https://travis-ci.org/account/repositories) and use the `Sync Account` button to sync your Travis account with GitHub.%%
1. Activate the repo using the slider switch in front of it.
  <include src="screenshot.md" boilerplate var-alt="Activate Repo" var-file="travisActivateRepo.png" inline />
  </tab>
</tabs>

#### Configuring your repository in Travis CI

1. [Generate a GitHub personal access token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/#creating-a-token) with **repo** permissions. Take note of the generated token - you will not be able to see it again once you navigate away from the page.
<include src="screenshot.md" boilerplate var-alt="GitHub Token Repo Permissions" var-file="githubTokenRepoPermissions.png" inline />
1. [Add an environment variable in Travis CI](https://docs.travis-ci.com/user/environment-variables/#defining-variables-in-repository-settings) named `GITHUB_TOKEN`, with the value set to the personal access token generated in the previous step. ==Ensure that _Display value in the build log_ is set to _Off_.==
   <include src="screenshot.md" boilerplate var-alt="Add GITHUB_TOKEN" var-file="travisGithubToken.png" inline />
1. Add a `.travis.yml` file to instruct Travis CI to build and deploy the site when you push to the repository. An example `.travis.yml` file that can accomplish this is given below:
    ```yaml
    language: node_js
    node_js:
      - '8'
    install:
      - npm i -g markbind-cli
    script: markbind build && markbind deploy --travis
    branches:
      only:
      - master
    ```
    More information about `.travis.yml` can be found in the [Travis CI documentation](https://docs.travis-ci.com/).
1. Commit `.travis.yml` to your MarkBind repository and push the changes. Travis CI should begin to build your site.
1. Select the MarkBind repository on [Travis CI](https://travis-ci.com/auth) and [check the build status](https://docs.travis-ci.com/user/job-lifecycle/#breaking-the-build) to see if it is successful.
1. Once the build succeeds, your MarkBind site should be online at `http://<username|org>.github.io/<repo>` e.g., http://se-edu.github.io/se-book. Travis CI will automatically build and deploy changes to your site as you push new changes to the repository after a few seconds.<br>
  %%{{ icon_info }} You might have to go to the `Settings` of your repo and configure it to publish GitHub Pages from the `gh-pages` branch as MarkBind deploys to that branch by default.%%

<box>

{{ icon_info }} Note that when Travis CI is set up as explained above, it will use the latest version of MarkBind which may be a later version than the one you use locally.
* If you want Travis CI to use a specific version of MarkBind (eg. `v1.6.3`), change the `install` step in the `.travis.yml` given above to:
  ```yaml
  install:
    - npm i -g markbind-cli@1.63
  ```
* If you want to use the latest minor version automatically until the next major version (as major versions usually contain breaking changes), you can add a `^` in front of the version number. In the example below, Travis will use the latest version of MarkBind but will stop before `2.*`
  ```yaml
  install:
    - npm i -g markbind-cli@^1.63
  ```
</box>

<hr>

## Deploying to Netlify

**You can setup [<tooltip content="a platform for deploying static webpages (among other things)">Netlify</tooltip>](https://www.netlify.com/)
 to automatically build and deploy your site on their platform every time your GitHub repo is updated.**

Here are the steps to set up Netlify:

1. Go to https://app.netlify.com/ and sign up
1. Next go to https://app.netlify.com/account/sites and select `New site from Git`
1. Select your git provider
   <include src="screenshot.md" boilerplate var-alt="Create a new site" var-file="netlifyPreview1.png" inline />
1. Select your markbind site repository
   <include src="screenshot.md" boilerplate var-alt="Select repository" var-file="netlifyPreview2.png" inline />
1. Update the build settings as follows and hit `Deploy site`:
   - `Build Command`: `npm i markbind-cli -g && markbind build --baseUrl`
   - `Publish directory`: `_site`
   <include src="screenshot.md" boilerplate var-alt="Update build settings" var-file="netlifyPreview3.png" inline />

Now your site will be deployed on Netlify at the given address specified after deployment. It will be updated automatically when the default branch of your repo is updated.

Additionally, **when contributors make a pull request to your GitHub repo, you can _preview_ the updated site** at the bottom of the pull request by clicking on `details` link in the PR:
<include src="screenshot.md" boilerplate var-alt="Preview deploy" var-file="netlifyPreview4.png" inline />

{{ icon_info }} Note that when Netlify is set up as explained above, it will use the latest version of MarkBind which may be a later version than the one you use locally. If you want Netlify to use a specific version of Netlify, follow the instructions in the box below:

<box>

##### Setting up Netlify to use a specific version of MarkBind

Here are the steps to set up Netlify to use a specific version of MarkBind.

1. Navigate to the root directory of your site.
1. run `npm init` which will create `package.json` and `package.lock.json`
1. run `npm install markbind-cli@1.6.3 --save` to install markbind as a dependency (using v1.6.3 as an example)
1. create/update `.gitignore` file in the root directory and add:
   ```
   node_modules
   ```
1. update `ignore` in site.json to include
   ```
   node_modules/*
   .gitignore
   ```

1. Now, follow the [previous instructions for setting up Netlify](#deploying-to-netlify) but with the following difference:<br>
   In step 5, ==Set the `Build Command` to `markbind build --baseUrl`==

</box>

{% from "njk/common.njk" import previous_next %}
{{ previous_next('makingTheSiteSearchable', 'markBindInTheProjectWorkflow') }}
