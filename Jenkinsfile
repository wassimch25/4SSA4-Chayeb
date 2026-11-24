pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        // Format imposé par l'énoncé : nomprenom_classe_nomProjet (Docker Hub exige des minuscules)
        registry = "DOCKERHUB_USER/chayebwassim-4ssa4-gestion-projets"
        registryCredential = 'dockerHub'
        dockerImage = ''
    }

    stages {

        stage('GIT') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                checkout scm
            }
        }

        stage('MVN CLEAN') {
            steps {
                echo 'Cleaning Maven project...'
                dir('backend') {
                    sh 'mvn clean'
                }
            }
        }

        stage('ARTIFACT CONSTRUCTION') {
            steps {
                echo 'Building Spring Boot application...'
                dir('backend') {
                    sh 'mvn package -DskipTests'
                }
            }
        }

        stage('UNIT TESTS') {
            steps {
                echo 'Running unit tests...'
                dir('backend') {
                    sh 'mvn test'
                }
            }
        }

        // stage('SONARQUBE ANALYSIS') : sera ajouté quand le serveur SonarQube sera installé
        // stage('SONARQUBE ANALYSIS') {
        //     steps {
        //         dir('backend') {
        //             withSonarQubeEnv('SonarQube') {
        //                 sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.5.0.6356:sonar -Dsonar.projectKey=gestion-projets'
        //             }
        //         }
        //     }
        // }

        stage('BUILD DOCKER IMAGE') {
            steps {
                echo 'Building Docker image...'
                script {
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }

        stage('PUSH TO DOCKER HUB') {
            steps {
                echo 'Pushing image to Docker Hub...'
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('CLEAN DOCKER IMAGE') {
            steps {
                sh "docker rmi ${registry}:${BUILD_NUMBER} || true"
            }
        }
    }
}
