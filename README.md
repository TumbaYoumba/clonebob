# Video guide

[![Video guide](https://img.youtube.com/vi/DLN1kNh_Ha0/0.jpg)](https://www.youtube.com/watch?v=DLN1kNh_Ha0 "Video guide")

# Step-by-step guide on setup

1. Make a shared group in your corporate gitlab
2. Create a project in that group (`pipelines` here)
3. Git push this content to your project
4. Edit `common/variables.yml` file with proper urls in `SEC_DD_URL` and `SEC_MOBILE_MOBSF_URL` (if you want to use an external mobsf). 
5. You need to edit the `SEC_PATH_TO_IMAGES` variable in `common/variables.yml` (it's just a project path to security images project).
6. And the same value should be written in `pipelines.yml` file to set `.image` include directive properly (We will remove this step later)
(image: $CI_REGISTRY/whitespots-public/security-images/toolset:latest)
7. Set `SEC_DD_KEY` in your project group. (get this token from your DefectDojo instance)
8. Now you can use the following example:


## To scan your code without passing any parameters

It will detect all languages/technologies automatically and run checks without parameters

```yml
stages: 
  # after build stage
  - security

security:
  stage: security
  allow_failure: true
  trigger:
    include:
    # Path to the shared repo
      - project: 'whitespots-public/pipelines'
        # a proper branch name
        ref: 'main'
        file: 'pipelines.yml'
```

## To scan your code with specific parameters

Look [here](integration_templates/detailed_integration.yml) if you need more parameters

```yml
stages: 
  # after build stage
  - security

security:
  stage: security
  allow_failure: true
  trigger:
    include:
    # Path to the shared repo
      - project: 'whitespots-public/pipelines'
        # a proper branch name
        ref: 'main'
        file: 'pipelines.yml'
  variables:

    # Secrets settings
    SEC_SECRETS_SCAN_ENABLE: "true"

    # SAST settings
    SEC_SAST_ENABLE: "true"
    SEC_GREP: "true"
    SEC_SEMGREP_CONFIG: "p/ci"
    SEC_PYTHON: "true"
    SEC_RUBY: "false"
    SEC_PHP: "false"
    SEC_JS: "true"
    SEC_GOLANG: "false"
    
    # Dependency check settings
    SEC_IMAGE_SCAN_ENABLE: "true"
    SEC_IMAGE_TO_SCAN_NAME: ${CI_REGISTRY_IMAGE}
    SEC_IMAGE_TO_SCAN_TAG: ${CI_COMMIT_REF_NAME}
    SEC_IMAGE_REGISTRY_URL: ${CI_REGISTRY}
    SEC_IMAGE_REGISTRY_USER: ${CI_REGISTRY_USER}
    SEC_IMAGE_REGISTRY_PASS: ${CI_REGISTRY_PASSWORD}

    # DAST settings
    SEC_DAST_ENABLE: "true"
    SEC_DAST_URL_TO_SCAN: "https://example.com"

    # Mobile settings
    SEC_MOBILE_ENABLE: "false"
    SEC_MOBILE_APK_PATH: "./apk/diva-beta.apk"
    SEC_MOBILE_SCAN_TYPE: "apk|ios"
    SEC_CHECKKARLMARX_DOMAIN: 'mycompany.com'
    SEC_CHECKKARLMARX_QA_TAGS: 'qa test dev stage'
    SEC_CHECKKARLMARX_PACKAGES: 'com.mycompany com.example'

    # Infrastructure scanners
    SEC_INFRA_ENABLE: "false"
    SEC_INFRA_DOMAIN: "exxxxample.com"

    # SAST (csharp) settings
    SEC_SONARQUBE_ENABLE: "false"
    SEC_SONARQUBE_URL: "https://sonarqube-test.whitespots.io"
    SEC_SONARQUBE_PROJECT_KEY: "test-app"
    SEC_SONARQUBE_SOURCES: "/usr/src"
    SEC_SONARQUBE_TOKEN: "token"
    SEC_SONARQUBE_PATH_TO_SLN: "token" # <- put in CI/CD variables for security reasons
    

```



# Add new scanner

1. Put scanner version to `common/variables.yml` (**pipelines** repo)
2. Add Dockerfile to folder in (**security-images** repo)
3. Add job to gitlab-ci.yml (**security-images** repo)
4. Add job with scan script in .gitlab folder (**pipelines** repo)
5. Configure default variables in `common/variables.yml` (**pipelines** repo)
6. Check all
