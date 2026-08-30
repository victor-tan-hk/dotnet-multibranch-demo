pipeline {

    agent any

    /*
     * Disable Declarative Pipeline's automatic SCM checkout.
     *
     * A Multibranch Pipeline normally performs an automatic checkout
     * before the first stage. We disable it here because we want to show
     * an explicit "SCM Checkout" stage in the workshop.
     */
     
    options {
        skipDefaultCheckout(true)
    }

    environment {

        SOLUTION_FILE = 'PortableCliApp.slnx'

        APP_PROJECT =
            'src/PortableCliApp/PortableCliApp.csproj'

        PUBLISH_DIRECTORY =
            "${WORKSPACE}/artifacts/publish"

        TEST_RESULTS_DIRECTORY =
            "${WORKSPACE}/artifacts/test-results"
    }

    stages {

        stage('SCM Checkout') {
            steps {

                /*
                 * Checkout the SCM revision selected by the Multibranch
                 * Pipeline.
                 *
                 * Depending on the Jenkins child job, this may represent:
                 *
                 * - master
                 * - a feature branch
                 * - a pull-request revision
                 *
                 * This is different from explicitly cloning a fixed repository
                 * URL, because Jenkins already knows which SCM head/revision
                 * the current Multibranch job is supposed to build.
                 */

                checkout scm
            }
        }

        stage('Clean') {
            steps {

                /*
                 * Same as previous Jenkinsfile 
                 */
                sh '''

                    rm -rf "${PUBLISH_DIRECTORY}"
                    rm -rf "${TEST_RESULTS_DIRECTORY}"

                '''
            }
        }

        stage('Restore') {
            steps {

                /*
                 * Same as previous Jenkinsfile 
                 */
                sh '''

                    dotnet restore "$SOLUTION_FILE"

                '''
            }
        }

        stage('Build') {
            steps {

                /*
                 * Same as previous Jenkinsfile 
                 */
                sh '''

                    dotnet build "$SOLUTION_FILE" \
                        --configuration Release \
                        --no-restore

                '''
            }
        }

        stage('Test') {
            steps {

                /*
                 * Same as previous Jenkinsfile 
                 */
                sh '''

                    mkdir -p "${TEST_RESULTS_DIRECTORY}"

                    dotnet test "$SOLUTION_FILE" \
                        --configuration Release \
                        --no-build \
                        --no-restore \
                        --logger "trx" \
                        --results-directory "${TEST_RESULTS_DIRECTORY}"

                '''
            }
        }

        stage('Publish') {
            steps {
            
                /*
                 * Same as previous Jenkinsfile 
                 */
                sh '''

                    dotnet publish "$APP_PROJECT" \
                        --configuration Release \
                        --no-build \
                        --output "${PUBLISH_DIRECTORY}"

                    echo "Published files:"

                    find "${PUBLISH_DIRECTORY}" \
                        -maxdepth 2 \
                        -type f \
                        -print

                '''
            }
        }

        stage('Deploy') {
            steps {

                /*
                 * Same as previous Jenkinsfile 
                 */
                 
                sh '''

                    dotnet \
                        "${PUBLISH_DIRECTORY}/PortableCliApp.dll"

                '''
            }
        }
    }
}