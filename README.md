# che-ansible #

## Create a new ansible plugin for Code Ready Workspaces on OpenShift4 ##

Unfortunately there is currently no plugin for Ansible, so here is an example of how to add a VSCode plugin to CodeReady Workspaces.

## Requirement: ##
1. Installed OCP4 cluster
2. Admin rights
3. CodeReady Workspaces installed

## 1. Create a new SideContainers ##
Create a Docker file in which all required apps are installed

https://github.com/mcpdev80/che-ansible/blob/master/Dockerfile

## 2. Build the container e.g. with Quay.io ##
  * Create an account with Quay.io and log in
  * Create a new repository and go to Builds and click on "Start new Build"
  
Upload your Dockerfile and the build will be carried out immediately and stored in the repository

## Create a meta.yaml for the new plugin ##

* Go to the pod of the "plugin registry"
  * Via the GUI
    * Under Workloads -> Pods -> click on "plugin-registry-??????-????"
    * Go to the terminal
    * You are under /var/www/html (web server)
  * Via the CLI
    * Change to the NameSpace "workspaces"
    ```Shell
    oc project workspaces
    ```
    * List the pods
    ```Shell
    oc get pods
    ```
    * Go into the pod
    ```Shell
    oc rsh plugin registry-????????-????
    ```
  * Create the meta file
    * Go to v3/plugins
    * Here all publishers of the existing plugins are created as a directory
    * The publisher we need does not yet exist, so it must be created with mkdir
      * Structure for new plugins /publisher/app/version
      ```Shell
      mkdir -p vscoss/vscode-ansible/0.5.2
      mkdir -p vscoss/vscode-ansible/latest
      ```
    * Create the meta.yaml under vscoss/vscode-ansible/0.5.2
      * https://github.com/mcpdev80/che-ansible/blob/master/plugin/meta.yaml
    * We now copy the meta.yaml file into the directory vscoss/vscode-ansible/latest, so that an explicit version does not have to be specified
    ```Shell
    cp vscoss/vscode-ansible/0.5.2/meta.yaml vscoss/vscode-ansible/latest/meta.yaml
    ```
    * A latest.txt file must be created under vscoss / vscode-ansible / with a reference to the latest version
    ```Shell
    vi vscoss/vscode-ansible/latest.txt
    ```
      * only with the version as text 0.5.2
     
## Create the json entry for the index json from the plugin registry workspaces ##

* Under the directory /var/www/html/v3/plugins there is an index.json file which is responsible for listing the existing plugins
  * Here we have to add our new plugin
  ```Shell
  vi /var/www/html/v3/plugins/index.json
  ```
    * https://github.com/mcpdev80/che-ansible/blob/master/plugin/index.json

## Use the plugin in your workspace ##

  * Existing workspace
    * In your workspace go to the menu item "VIEW" and select "Plugin"
    * A list with the available plugins is displayed
    * Select the newly added plugin here and install it
    * The workspace will restart and the new plugin is added to your WorkSpace
  * Create and edit a new workspace
    * To edit to add our new plugins click on the arrow to the right of "Create & Open" and choose Create & Proceed Editing
    * To add our new plugin, click on the Plugins tab and select the new plugin
    * Click on Save
    * Start the workspace with "RUN" or "OPEN"
