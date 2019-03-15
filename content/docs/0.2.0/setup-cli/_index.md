---
title: Setup keptn CLI
description: The following description explains how to setup the keptn CLI and connect it to your keptn server.
weight: 16
keywords: [cli, setup]
---

In this section, the functionality and commands of the keptn CLI are described. The keptn CLI is needed to configure the keptn server, to create new projects and to onboard new services to the keptn server. Furthermore, authorization is needed for the keptn CLI against the keptn server.

<!--
For onboarding, a so-called `shipyard` (**TODO: provide more information/link here**) files has to be provided that defines deployment strategies for the service, as well as the different stages (i.e., dev, staging, and production).
During onboarding, keptn creates a new GitHub projects, which then contains branches for the specified stages (i.e. dev, staging, and production).
Furthermore, keptn creates resources definitions for several Kubernetes resources in terms on [Helm charts](https://helm.sh/).
Please note that onboarding does not deploy a service.
-->

If you are unfamiliar with keptn, we recommend to first watch this [community meeting recording](https://drive.google.com/open?id=1Zj-c0tGIvQ_0Dys6NsyDa-REsEZCvAHJ),
which provides an introduction to keptn.

## Prerequisites
- A successful keptn server installation (including the tools like kubectl, ...)
- A GitHub organization, a GitHub user, and [personal access token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line). 

## Install the keptn CLI
Every release of keptn provides binaries for the keptn CLI. These binaries are available for Linux, macOS, and Windows.

1. Download your [desired version](https://github.com/keptn/keptn/releases/)
1. Unpack the download
1. Find the `keptn` binary in the unpacked directory.
  - Linux / macOS
    
        add executable permissions (``chmod +x keptn``), and move it to the desired destination (e.g. `mv keptn /usr/local/bin/keptn`)

  - Windows

        move/copy the executable to the desired folder

1. Now, you should be able to run the keptn CLI by 
    ```console
    keptn --help
    ```

{{< popup_image
    link="./assets/keptn-cli-help.png"
    caption="keptn CLI">}}


## Start using the keptn CLI
**TODO:** Describe expected output or how the command can be verified

The keptn CLI allows to onboard a new service using the commands described in the following.
All of these commands provide a help flag (`--help`), which describes details of the respective command (e.g., usage of the command or description of flags).

**Note:** In the current version, keptn is missing checks whether the sent command is executed correctly.
In order to guarantee the expected behavior, please strictly use the following commands in the specified order.
In future releases, we add additional checks whether the executed commands succeeded or failed.

## keptn auth 

Before the keptn CLI can be used, it needs to be authenticated against a keptn server. Therefore, an endpoint and an API token are required. To retrieve them, execute the following commands:

### Linux / macOS

```console
$ KEPTN_API_TOKEN=$(kubectl get secret keptn-api-token -n keptn -o=yaml | yq - r data.keptn-api-token | base64 --decode)

$ KEPTN_ENDPOINT=https://$(kubectl get ksvc -n keptn control -o=yaml | yq r - status.domain)
```

### Windows 

Please expand the corresponding section matching your CLI tool.

<details><summary>PowerShell</summary>
<p>

For the Windows PowerShell, a small script is provided that installs the `PSYaml` module and sets the environment variables. Please note that the PowerShell might have to be started with **Run as Administrator** privileges to install the module.

Copy the following snippet in a file `set-keptn-env-variables.ps1` and run it with `.\set-keptn-env-variables.ps1` from your PowerShell.

```
Install-Module PSYaml
import-module psyaml
$yamlText = kubectl get secret keptn-api-token -n keptn -o=yaml
$content = ''
foreach ($line in $yamlText) { $content = $content + "`n" + $line }
$yaml = ConvertFrom-YAML $content
$Env:KEPTN_API_TOKEN = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($yaml.data."keptn-api-token"))

$yamlText = kubectl get ksvc -n keptn control -o=yaml
$content = ''
foreach ($line in $yamlText) { $content = $content + "`n" + $line }
$yaml = ConvertFrom-YAML $content
$ENDPOINT = $yaml.status.domain
$Env:KEPTN_ENDPOINT = "https://$ENDPOINT"
```

</p>
</details>

<details><summary>Command Line</summary>
<p>

In the Windows Command Line, a couple of steps are necessary.

1. Get the keptn API Token encoded in base64

    ```console
    $ kubectl get secret keptn-api-token -n keptn -o=yaml

    Output:
    apiVersion: v1
    data:
      keptn-api-token: abcdefghijkladfaea
    kind: Secret
    metadata:
      ...
    type: Opaque
    ```

1. Take the encoded API token - it is the value from the key `keptn-api-token`, in this case `abcdefghijkladfaea` and save it in a text file, e.g., `keptn-api-token-base64.txt`

1. Decode the file

    ```
    $ certutil -decode keptn-api-token-base64.txt keptn-api-token.txt
    ```

1. Open the newly created file `keptn-api-token.txt`, copy the value and paste it into the next command

    ```
    $ set KEPTN_API_TOKEN=value-of-your-token
    ```

1. Get the keptn server endpoint 

    ```
    $ kubectl get ksvc -n keptn control -o yaml

    Output:
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    ...
    status:
      address:
        hostname: control.keptn.svc.cluster.local
      ...
      domain: control.keptn.XX.XXX.XXX.XX.xip.io
      ...
    ```

1. Copy the `domain` value and save it in an environment variable

    ```
    $ set KEPTN_ENDPOINT=https://control.keptn.XX.XXX.XXX.XX.xip.io
    ```

</p>
</details>

Now that everything we need is stored in environment variables, we can proceed with authorizing the keptn CLI.

If the authentication is successful, keptn will inform the user. Furthermore, if the authentication is successful, the endpoint and the API token are stored in a password store of the underlying operating system.
More precisely, the keptn CLI stores the endpoint and API token using `pass` in case of Linux, using `Keychain` in case of macOS, or `Wincred` in case of Windows.

To authenticate against the keptn server use command `auth` and your endpoint and API token:

- Linux/macOS

```console
$ keptn auth --endpoint=$KEPTN_ENDPOINT --api-token=$KEPTN_API_TOKEN
```

- Windows

```
$ keptn.exe auth --endpoint=%KEPTN_ENDPOINT% --api-token=%KEPTN_API_TOKEN%
```

**Note**: If you receive a warning `Using a file-based storage for the key because the password-store seems to be not set up.` it is because a password store could not be found in your environment. In this case, the credentials are stored in a file called `.keptn` in your home directory.


## keptn configure 

In order to work with GitHub (i.e. create a new project, make commits), keptn requires a
GitHub organization, the GitHub user, and the GitHub personal access token belonging to that user.
Therefore, the keptn CLI is used to set the GitHub organization, the GitHub user, and the GitHub personal access token belonging to that user in the keptn server.

<span style="color:red">
**Note:** Should we describe a best practice, which creates a new GitHub user only used by keptn?
</span>

To configure the used GitHub organization, user, and personal access token use command `configure` and provide your details using the respective flags:

```console
$ keptn configure --org=gitHubOrg --user=gitHub_keptnUser --token=XYZ
```

## keptn create project 

Before onboarding a service, a project needs to be created. A project represents a repository in the GitHub organization that is used by keptn. This project will contain a branch for each stage of the multi-stage environment (e.g., dev, staging, and production stage). In other words, the separation of stage configurations is based on repository branches. To describe each stage, a `shipyard.yaml` file is needed that specifies the name, deployment strategy, and test strategy as shown below:

```yaml
stages:
  - name: "dev"
    deployment_strategy: "direct"
    test_strategy: "functional"
  - name: "staging"
    deployment_strategy: "direct"
    test_strategy: "performance"
  - name: "production"
    deployment_strategy: "blue_green_service"
```

To create a new project, use the command `create project` and specify the name of the project as well as the `shipyard.yaml` file.

```console
$ keptn create project sockshop shipyard.yml
```

## keptn onboard service

After creating a project which represents a repository in your GitHub organization, the keptn&nbsp;CLI allows to onboard services into this project. Please note that for describing the Kubernetes resources, [Helm charts](https://helm.sh/) are used. Therefore, the keptn CLI allows setting a Helm values description in the before created project. Optionally, the user can also provide a Helm deployment and service description.

To onboard a service, use the command `onboard service` and provide the project name, the Helm chart values and optionally also deployment and service descriptions.

```console
$ keptn onboard service --project=sockshop --values=values.yaml
```
or
```console
$ keptn onboard service --project=sockshop --values=values.yaml --deployment=deployment.yaml --service=service.yaml
```

To start onboarding a service, please see the [Use Case section](../usecases/onboard-carts-service).
