# Day 78: Jenkins Conditional Pipeline

## Challenge Description
The goal is to create a Jenkins pipeline job named `nautilus-webapp-job` that conditionally deploys a static website to the Nautilus App Servers. The deployment target is the Storage Server (`ststor01`), which shares its `/var/www/html` directory with the App Servers. The pipeline must take a `BRANCH` parameter and deploy either the `master` or `feature` branch from the `web_app` Gitea repository based on the input.

## Key Concepts
- Jenkins Pipeline
- Parameterized Builds (String Parameter)
- Jenkins Agents (Slave Nodes)
- Conditional Deployment
- Git Cloning
- Shell Scripting within Jenkins

## Solution

1.  **Configure Storage Server Node:**
    -   We added a new permanent agent named `Storage Server`.
    -   Label: `ststor01`.
    -   Remote root directory: `/var/www/html`.
    -   Launch method: SSH with credentials for user `natasha` on host `ststor01`.

2.  **Create Pipeline Job:**
    -   We created a Pipeline job named `nautilus-webapp-job`.
    -   We enabled "This project is parameterized" and added a String Parameter named `BRANCH`.
    -   We used the following Pipeline script:

```groovy
pipeline {
    agent { label 'ststor01' }

    stages {
        stage('Deploy') {
            steps {
                script {
                    sh '''
                        rm -rf /tmp/web_app
                        git clone -b ${BRANCH} http://git.stratos.xfusioncorp.com/sarah/web_app.git /tmp/web_app
                        ls -la /tmp/web_app
                        echo 'Bl@kW' | sudo -S cp -r /tmp/web_app/* /var/www/html/
                        rm -rf /tmp/web_app
                    '''
                }
            }
        }
    }
}
```

## Verification

1.  **Deploy Master:**
    -   We ran the build with `BRANCH` set to `master`.
    -   We accessed the App URL to verify the master branch content.

2.  **Deploy Feature:**
    -   We ran the build with `BRANCH` set to `feature`.
    -   We accessed the App URL to verify the content updated to the feature branch.

3.  **Check Files on Server:**
    -   We verified file existence on `ststor01` in `/var/www/html`:
        ```bash
        [natasha@ststor01 html]$ ls
        caches  feature.html  index.html  remoting  remoting.jar  workspace
        [natasha@ststor01 html]$ cat feature.html
        Added new feature
        [natasha@ststor01 html]$ cat index.html
        Updated
        ```
