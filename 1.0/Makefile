AWS_ACCESS_KEY=<your_aws_access_key>
AWS_REGION=us-east-1
AWS_SECRET_KEY=<your_aws_secret_key>

BUILD_TAG=1.0

DOCKER_HUB_USER=<your_dockerhub_user>
DOCKER_HUB_PASS=<your_dockerhub_pass>

GCP_ZONE=us-central1
GCP_PROJECT_ID=<your_gcp_project_id>

default = build

clean:
	rm -f variables.json variables-min.json

variables-min: variables.template
	sed \
		-e 's!{{ .AWS_Access_Key }}!'"$(AWS_ACCESS_KEY)"'!g' \
		-e 's!{{ .AWS_Region }}!'"$(AWS_REGION)"'!g' \
		-e 's!{{ .AWS_Secret_Key }}!'"$(AWS_SECRET_KEY)"'!g' \
		-e 's!{{ .Build_Tag }}!'"$(BUILD_TAG)-minimal"'!g' \
		-e 's!{{ .Docker_Hub_Pass }}!'"$(DOCKER_HUB_PASS)"'!g' \
		-e 's!{{ .Docker_Hub_User }}!'"$(DOCKER_HUB_USER)"'!g' \
		-e 's!{{ .GCP_Zone }}!'"$(GCP_ZONE)"'!g' \
		-e 's!{{ .GCP_Project_Id }}!'"$(GCP_PROJECT_ID)"'!g' \
		-e 's!{{ .Script_Path }}!'"scripts/setup-minimal.sh"'!g' \
		variables.template > variables-min.json

variables: variables.template
	sed \
		-e 's!{{ .AWS_Access_Key }}!'"$(AWS_ACCESS_KEY)"'!g' \
		-e 's!{{ .AWS_Region }}!'"$(AWS_REGION)"'!g' \
		-e 's!{{ .AWS_Secret_Key }}!'"$(AWS_SECRET_KEY)"'!g' \
		-e 's!{{ .Build_Tag }}!'"$(BUILD_TAG)"'!g' \
		-e 's!{{ .Docker_Hub_Pass }}!'"$(DOCKER_HUB_PASS)"'!g' \
		-e 's!{{ .Docker_Hub_User }}!'"$(DOCKER_HUB_USER)"'!g' \
		-e 's!{{ .GCP_Zone }}!'"$(GCP_ZONE)"'!g' \
		-e 's!{{ .GCP_Project_Id }}!'"$(GCP_PROJECT_ID)"'!g' \
		-e 's!{{ .Script_Path }}!'"scripts/setup.sh"'!g' \
		variables.template > variables.json

.PHONY: build
build: scripts/setup.sh scripts/setup-minimal.sh variables variables-min ## Builds all the images defined in the Packer template
	packer build -var-file=variables.json template.json
	packer build -var-file=variables-min.json template.json

.PHONY: google-min
google-min: scripts/setup-minimal.sh variables-min ## Builds only the Google Compute minimal image defined in the Packer template
	packer build -only=googlecompute -var-file=variables-min.json template.json

.PHONY: google
google: scripts/setup.sh variables ## Builds only the Google Compute image defined in the Packer template
	packer build -only=googlecompute -var-file=variables.json template.json

.PHONY: amazon-min
amazon-min: scripts/setup-minimal.sh variables-min ## Builds only the Amazon EBS minimal image defined in the Packer template
	packer build -only=amazon-ebs -var-file=variables-min.json template.json

.PHONY: amazon
amazon: scripts/setup.sh variables ## Builds only the Amazon EBS image defined in the Packer template
	packer build -only=amazon-ebs -var-file=variables.json template.json

.PHONY: docker-min
docker-min: scripts/setup-minimal.sh variables-min ## Builds only the Docker minimal image defined in the Packer template
	packer build -only=docker -var-file=variables-min.json template.json

.PHONY: docker
docker: scripts/setup.sh variables ## Builds only the Docker image defined in the Packer template
	packer build -only=docker -var-file=variables.json template.json

.PHONY: help
help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'
