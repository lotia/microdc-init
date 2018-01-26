[![Build Status](https://travis-ci.org/EqualExpertsMicroDC/ee-microdc-init.svg)](https://travis-ci.org/EqualExpertsMicroDC/ee-microdc-init)

# ee-microdc-init
A python package to manage a pick 'n' mix Kubernetes infrastructure on AWS

### Install
`pip install git+https://github.com/EqualExpertsMicroDC/ee-microdc-init`

#### or run with docker
`docker build -t microdc .`
`docker run microdc microdc --help`

### Usage
`microdc --help`

### Tests
`$ ./test.sh`


### Bring up a MicroDC environment
This will bring up kubernetes clusters in AWS accounts.  In this example we will spin up a single cluster called dev.

### Meet the prerequisites
 * Two accounts with AWS - prod and nonprod (you can also go with one)
 * Delegate a domain or sub domain
 * Generate a domain certificate from ACM and save the arn for use in the config file below - [howto](https://github.com/EqualExpertsMicroDC/ee-microdc-init/blob/master/docs/configure_acm_cert.md)
 * We have found raising the following AWS limits helpful (based on a environment setup of dev, test, stage, prod) :
   - Raise the Ec2 instance limit to 100
   - Increase elastic IPs to 20
   - Raise the VPC limit to 20
 * Install terraform - Download the relevant binary for your operating system [here](https://www.terraform.io/downloads.html)
 * Install kops - Download the relevant binary for your operating system [here](https://github.com/kubernetes/kops/releases/tag/1.8.0)
 * Install kubectl - Follow [these](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-via-curl) steps
 * Install AWS CLI - Instruction [here](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)

### Then follow these steps

1. First we create a config.yml
```
cat > config.yml << EOF
TODO
EOF
```
2. We run the global setup
```
microdc --config config.yml --account prod --stack global --tool terraform up --bootstrap
microdc --config config.yml --account prod --stack global --tool terraform up --bootstrap | sh
```

3. Initial cluster setup
```
microdc --config config.yml --account prod --tool kops up --env prod
microdc --config config.yml --account prod --tool kops up --env prod | sh
```

4. Complete setup around the edges.
```
microdc --config config.yml --account prod --stack service --tool terraform up --env prod
microdc --config config.yml --account prod --stack service --tool terraform up --env prod | sh
```

5. Validate our setup.
`kops validate cluster`

6. Deploy stack.
```
microdc --config config.yml --account prod --tool kubectl up --env prod
microdc --config config.yml --account prod --tool kubectl up --env prod | sh
```

### Extras - create developer and deployment users for the apps namespace
https://github.com/EqualExpertsMicroDC/apps-namespace
