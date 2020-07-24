---
date: "2020-07-21T16:03:07+02:00"
title: "keptn create project"
slug: keptn_create_project
---
## keptn create project

Creates a new project

### Synopsis

Creates a new project with the provided name and shipyard file. 
The shipyard file describes the used stages. These stages are defined by name, 
deployment-, test-, and remediation strategy.

By executing the *create project* command, Keptn initializes an internal Git repository that is used to maintain all project-related resources. 
To upstream this internal Git repository to a remote repository, the Git user (*--git-user*), an access token (*--git-token*), and the remote URL (*--git-remote-url*) are required.

For more information about shipyard files, creating projects, or upstream repositories, please go to [Manage Keptn](https://keptn.sh/docs/0.7.x/manage/)


```
keptn create project PROJECTNAME --shipyard=FILEPATH [flags]
```

### Examples

```
keptn create project PROJECTNAME --shipyard=FILEPATH
keptn create project PROJECTNAME --shipyard=FILEPATH --git-user=GIT_USER --git-token=GIT_TOKEN --git-remote-url=GIT_REMOTE_URL
```

### Options

```
  -r, --git-remote-url string   The remote url of the upstream target
  -t, --git-token string        The git token of the git user
  -u, --git-user string         The git user of the upstream target
  -h, --help                    help for project
  -s, --shipyard string         The shiypard file specifying the environment
```

### Options inherited from parent commands

```
      --mock                 mocking of server communication - ATTENTION: your commands will not be sent to the keptn server
  -q, --quiet                suppress debug and info output
      --suppress-websocket   disables websocket communication - use the ID of Keptn context (if provided) for checking the result of your command
  -v, --verbose              verbose logging
```

### SEE ALSO

* [keptn create](../keptn_create/)	 - 

###### Auto generated by spf13/cobra on 21-Jul-2020