# Hatchbox Deployment for Targets

All of our project targets are deployed to [Hatchbox](https://hatchbox.io/) in a "firstdraft" team. Hatchbox acts as a management layer to the [Digital Ocean](https://cloud.digitalocean.com/projects) "FirstDraft" team where the apps run on a pair of servers in the "matchthetarget" project.

This guide demonstrates how to:

- create a target,
- deploy the target on Hatchbox,
- and manage the app through shell access.

## Create a target

All targets are hosted at [github.com/matchthetarget](https://github.com/matchthetarget).

Create a new _private_ repository (so students cannot access the solution code) and build the app in that organization.

You can use our Rails 7 template as a starting point: [github.com/appdev-projects/rails-7-template](https://github.com/appdev-projects/rails-7-template).

## Deploy to Hatchbox

Once you have a developed app in the [github.com/matchthetarget](https://github.com/matchthetarget) organization, here are the steps to deploy:

1. Login to the "firstdraft" team and visit [https://app.hatchbox.io/apps](https://app.hatchbox.io/apps) and click on "New App"

    ![](/assets/hatchbox-new-app.png)
    {: .bleed-full }

2. The cluster should be "matchthetarget (digitalocean)" and the name should match the name of the repo. Then "Create App".

    ![](/assets/hatchbox-new-app-name.png)
    {: .bleed-full }

3. The first step on the new app dashboard in the "Repository" tab is to select "Matchthetarget (Github App)" from the Git host dropdown, and then provide the path to the app in the organization (e.g. `matchthetarget/<my-repo-name>`). Then "Save Changes".

    ![](/assets/hatchbox-new-app-repo-connect.png)
    {: .bleed-full }

4. The Save will redirect you to the "Dashboard" tab, but you should go back to the "Repository" tab and scroll down to "Automatic Deploys" and click "Enable automatic deploys" so that pushes to the repo will trigger new deployments.

5. _If_ the app is database-backed, then visit the "Databases" tab and click the "New Postgresql" button. That's all you need to create and connect the database. _If_ the app is _NOT_ database-backed, then you should visit the "Environment" tab and add this ENV variable: `HATCHBOX_SKIP_MIGRATE=1` ([source](https://hatchbox.relationkit.io/articles/42-how-does-hatchbox-run-rails-migrations)).

6. Add any other ENV variables that your app needs in the "Environment" tab.

7. _If_ the app has a cron job that should run regularly, go to the "Cron Jobs" tab and "Add Cron Job". Here is an example for adding an hourly `sample_data` task (**Note:** the double quotes on `"sample_data"` were found to be necessary):

    ![](/assets/hatchbox-new-app-cron-job.png)
    {: .bleed-full }

8. In the "Domains & SSL" tab, add the domain that you want to deploy the target to. You will then need to visit your DNS registrar and add an `A` record pointing to the IP of the Digital Ocean server: `146.190.209.166`

    ![](/assets/hatchbox-new-app-add-domain.png)
    {: .bleed-full }

9. You are now ready to "Deploy" the app. Click on the top right button for "Deploy", which can be found on all tabs in the dashboard. You can monitor the deployment logs in the "Activity" tab. When the app is deployed you can "View App" (also in the top right portion of the dashboard) at a designated Hatchbox URL, and your app will also shortly be live at the selected domain from the previous step. Subsequent deploys will be automatic on repo pushes if you selected that option in the "Repository" tab.

## Manage Digital Ocean

Since the apps are running on the Digital Ocean designated "droplets" (servers), you may need to occasionally manage these.

Hatchbox will notify you when a server is "outdated", in which case you can visit [https://app.hatchbox.io/clusters](https://app.hatchbox.io/clusters), click through to "View" each server, and click the "Configure" button, which will apply updates without the need to visit Digital Ocean.

On our Digital Ocean project, there are email alerts set for high memory and CPU usage. If these limits are being exceeded, you may need to "resize" the droplet to increase the resources. [Instructions to do so via the Digital Ocean dashboard are here](https://docs.digitalocean.com/products/droplets/how-to/resize/).

## SSH Access to the Target

To access the `rails console` and other app-related maintenance at the command-line, visit [https://app.hatchbox.io/ssh_keys](https://app.hatchbox.io/ssh_keys) and follow the instructions to add a "New SSH Key" for your laptop or computer. More information on SSH access can be found [in the Hatchbox guides](https://hatchbox.gitbook.io/hatchbox/servers).

Once you have setup your SSH keys, at a terminal you can run:

```
ssh deploy@X.X.X.X
```

Where `X.X.X.X` is the public IP of the Digital Ocean server. For us, that is:

```
ssh deploy@146.190.209.166
```

You will be shown the list of apps on the server and some shortcut commands to view app server logs and enter a `rails console`.

![](/assets/hatchbox-ssh-login.png)
{: .bleed-full }

To enter one of the app's current codebases, you can:

```
deploy@icy-frost-7628:~$ cd msm-gui-demo/current/
```

The `<app-name>/current` folder contains all source code, and you can do things like:

![](/assets/hatchbox-ssh-sample-data.png)
{: .bleed-full }

if you need to manually reset the sample data outside of a cron job run.

---
