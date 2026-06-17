@Library('shared-library') _

@Library('ot-central-ci') _

def cipipeline = new opstree.ci.templates.java_ci.java_ci()

node {
    cipipeline.call([

        // ── WORKSPACE MANAGEMENT ────────────────────────────────────────────
        clean_workspace                 : true,
        ignore_clean_workspace_failure  : false,
        delete_dirs                     : true,
        clean_when_build_aborted        : true,
        clean_when_build_failed         : true,
        clean_when_not_built            : true,
        clean_when_build_succeed        : true,
        clean_when_build_unstable       : true,

        // ── VCS / GIT CHECKOUT ──────────────────────────────────────────────
        // Using HTTP + token-based authentication (jenkins_git_creds_id holds the token credential)
        repo_url_type                   : 'http',
        repo_https_url                  : 'https://github.com/your-org/your-app.git',
        repo_ssh_url                    : '',
        repo_branch                     : 'main',
        source_code_path                : '',                                       // sub-directory inside repo; leave '' for root
        jenkins_git_creds_id            : 'github-token-credentials-id',           // Jenkins credential ID (secret text / user+token)
        jenkins_git_ssh_key_id          : '',
        ssh_private_key_location        : '',

        // ── BUILD PARAMETER OVERRIDE ────────────────────────────────────────
        // Set true to allow Jenkins build parameters to override any value at runtime
        enable_jenkins_build_param_override : false,

        // ── PRE-BUILD: CREDENTIALS SCANNING (Gitleaks) ─────────────────────
        gitleaks_check                  : false,
        fail_job_if_leak_detected       : true,
        gitleaks_report_format          : 'json',
        gitleaks_report_jenkins_publish : true,

        // ── PRE-BUILD: DEPENDENCY SCANNING (OWASP) ─────────────────────────
        dependency_check                          : false,
        dependency_scan_tool                      : 'owasp',
        owasp_project_name                        : 'your-app',
        owasp_report_publish                      : true,
        owasp_report_format                       : 'HTML',
        fail_job_if_dependency_returned_exception : false,
        pom_location                              : '',
        app_stack                                 : 'java',

        // ── BUILD ARTIFACT (Gradle) ─────────────────────────────────────────
        // build_artifact.groovy runs Gradle inside Docker based on java_version:
        //   java_version=11  → gradle:7.5-jdk11
        //   java_version=17  → gradle:7.5-jdk17
        perform_code_build              : true,
        build_tool                      : 'gradle',
        java_version                    : '17',
        gradle_command                  : 'clean build',
        gradle_build_file_location      : '',                                       // sub-dir containing build.gradle; leave '' for root
        // Maven-specific (not used for Gradle — kept for completeness)
        withmaven_globaltool_jdk        : '',
        withmaven_globaltool_maven      : '',
        mvn_settings_path               : '',
        // AWS CodeArtifact (Gradle does not use this path in build_artifact.groovy; set false)
        codeartifact_dependency         : false,
        codeartifact_domain             : '',
        codeartifact_owner              : '',

        // ── STATIC CODE ANALYSIS (SonarQube) ───────────────────────────────
        static_code_analysis_check                : false,
        codebase_to_scan_directory                : '.',
        path_to_sonar_properties                  : 'sonar-project.properties',
        jenkins_sonarqube_token_creds_id          : 'sonarqube-token',
        fail_job_if_analysis_returned_exception   : false,

        // ── UNIT TESTING & CODE COVERAGE ───────────────────────────────────
        unit_testing_check              : false,
        fail_job_if_unit_issue_detected : false,
        unit_test_reports_path          : '**/build/test-results/test/*.xml',
        findbugs_test_report_path       : '**/build/reports/findbugs/*.xml',

        // ── BUILD DOCKER IMAGE ──────────────────────────────────────────────
        perform_build_dockerfile        : false,
        image_name                      : 'your-org/your-app',
        dockerfile_location             : 'Dockerfile',
        dockerfile_context              : '.',

        // ── POST-BUILD: IMAGE SCANNING (Trivy) ─────────────────────────────
        image_scanning_check            : false,
        image_tag                       : 'latest',
        scan_severity                   : 'HIGH,CRITICAL',
        image_scanning_report_publish   : true,

        // ── POST-BUILD: DOCKLE HARDENING SCAN ──────────────────────────────
        dockle_scan_check               : false,
        dockle_report_publish           : true,

        // ── POST-BUILD: IMAGE SIZE VALIDATOR ───────────────────────────────
        image_size_validator_check      : false,
        max_allowed_image_size          : '500',                                    // in MB
        fail_job_if_validation_fail     : false,

        // ── PUBLISH ARTIFACT ────────────────────────────────────────────────
        artifact_publish_check          : false,
        artifact_destination_type       : 'ECR',                                    // 'ECR' | 'Harbor' | 'S3'

        // ECR (used when artifact_destination_type=ECR)
        jenkins_aws_credentials_id      : 'aws-credentials',
        ecr_repo_name                   : 'your-app',
        ecr_region                      : 'us-east-1',
        account_id                      : '123456789012',

        // Harbor (used when artifact_destination_type=Harbor)
        harbor_url                      : 'https://harbor.your-org.com',
        harbor_project                  : 'your-project',
        harbor_credentials_id           : 'harbor-credentials',

        // S3 (used when artifact_destination_type=S3)
        artifact_source_path            : 'build/libs/your-app.jar',
        artifact_s3_bucket_name         : 'your-artifact-bucket',
        artifact_s3_bucket_aws_region   : 'us-east-1',

        // ── NOTIFICATIONS (Slack) ───────────────────────────────────────────
        notification_enabled            : false,
        webhook_url_creds_id            : 'slack-webhook',
        notification_channel            : 'slack',

        // ── JIRA INTEGRATION ────────────────────────────────────────────────
        enable_jira                         : false,
        jenkins_jira_url_env_name           : 'JIRA_URL',
        jenkins_jira_creds_id               : 'jira-credentials',
        fail_job_if_jira_operation_failed   : false,
        enable_buildlogurl_in_jiracomment   : true,

        // ── TRIGGER CD PIPELINE (on CI success) ─────────────────────────────
        enable_trigger_cd_pipeline      : false,
        trigger_cd_pipeline_path        : 'CD/your-app',                            // Jenkins job path of the CD job
        image_tag_build_param           : 'IMAGE_TAG',                              // param name expected by the CD job
        enable_jira_build_param         : 'ENABLE_JIRA',
        jira_ticket_id_build_param      : 'JIRA_TICKET_ID'

    ])
}

