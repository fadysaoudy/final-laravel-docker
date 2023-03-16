# Laravel with Docker

This is a sample Laravel project that is ready to be deployed to a Docker container using Jenkins automation.

## Getting Started
### Prerequisites
Before you can deploy this project to a Docker container, you will need the following:

#### Docker
#### Docker Compose
#### Jenkins

### Installing
1. Clone this repository to your local machine.
2. Create a new EC2 instance on AWS.
3. Install Docker and Jenkins on your EC2 instance.
4. Create a new pipeline in Jenkins and link it to your GitHub repository.
5. Configure the pipeline by adding the Jenkinsfile in this repository.

### Deployment
##### To deploy the project to a Docker container, follow these steps:

1. Open Jenkins and run the pipeline you created.
2. Jenkins will verify the tooling, clear any running Docker containers, and start Docker.
3. Jenkins will run Composer install and populate the .env file.
4. Jenkins will run tests on the Laravel application.
5. If all tests pass, Jenkins will create an artifact.zip file and upload it to your EC2 instance.
6. Jenkins will then unzip the artifact.zip file and deploy the project to the Docker container.

### Built With
- Laravel - The web framework used
- Docker - The containerization technology used
- Jenkins - The automation tool used 

### Authors
##### Fadi Saoudy

### Acknowledgments
[Jenkins documentation](https://www.jenkins.io/doc/)
[Docker documentation](https://docs.docker.com/)
[Laravel documentation](https://laravel.com/docs/10.x)
