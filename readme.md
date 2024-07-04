# Java Web Application CI/CD with AWS CodeCommit and AWS Elastic Beanstalk

This project demonstrates the setup of a CI/CD pipeline to host a Java web application using AWS CodeCommit and AWS Elastic Beanstalk. 

## Prerequisites

1. AWS Account
2. AWS CLI installed and configured
3. AWS CodeCommit repository
4. AWS Elastic Beanstalk environment

## Project Structure

```
my-java-app/
├── src/
│   └── main/
│       └── java/
│       └── resources/
│           └── application.properties
├── target/
│   └── vprofile-v2/
│       └── *.jar
├── pom.xml
├── buildspec.yml
└── README.md
```

## Steps

### 1. Create AWS CodeCommit Repository

1. Log in to AWS Management Console.
2. Navigate to CodeCommit.
3. Create a new repository.

### 2. Clone the Repository

```bash
git clone https://git-codecommit.<region>.amazonaws.com/v1/repos/<repository-name>
cd <repository-name>
```

### 3. Add Your Project Files

Copy your Java project files into the cloned repository directory.

### 4. Create `buildspec.yml`

Add the following `buildspec.yml` file to the root of your repository:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto8
  pre_build:
    commands:
      - apt-get update
      - apt-get install -y jq 
      - wget https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz
      - tar xzf apache-maven-3.9.8-bin.tar.gz
      - ln -s apache-maven-3.9.8 maven
      - sed -i 's/jdbc.password=admin123/jdbc.password=eAsulc31MwYLeps1RjlV/' src/main/resources/application.properties
      - sed -i 's/jdbc.username=admin/jdbc.username=admin/' src/main/resources/application.properties
      - sed -i 's/db01:3306/vprofile-prod-1.c7acco4egrzn.us-west-1.rds.amazonaws.com:3306/' src/main/resources/application.properties
  build:
    commands:
      - mvn install
  post_build:
    commands:
       - mvn package
artifacts:
  files:
     - '**/*'
  base-directory: 'target/vprofile-v2'
```

### 5. Commit and Push Your Code

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

### 6. Create and Configure AWS Elastic Beanstalk Environment

1. Navigate to Elastic Beanstalk in the AWS Management Console.
2. Create a new environment and choose the appropriate platform for your Java application.
3. Configure the environment settings as needed.

### 7. Set Up AWS CodePipeline

1. Navigate to CodePipeline in the AWS Management Console.
2. Create a new pipeline and follow the steps:
   - **Source**: Select CodeCommit and choose your repository and branch.
   - **Build**: Select AWS CodeBuild and configure a new build project using the `buildspec.yml` file.
   - **Deploy**: Select AWS Elastic Beanstalk and choose your environment.

### 8. Trigger the Pipeline

Make a change in your code and push it to the repository. This will trigger the CodePipeline and deploy your changes to Elastic Beanstalk.

## Conclusion

You have now set up a CI/CD pipeline to automatically build and deploy a Java web application to AWS Elastic Beanstalk using AWS CodeCommit and AWS CodePipeline.