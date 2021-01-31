# ___stfX_: SpatioTemporal Features eXtractor__


> :exclamation: This repository has been archived. Further developments on this repository are done in [most-inesctec/stfX](https://github.com/most-inesctec/stfX). :exclamation:


The __SpatioTemporal Features eXtractor (_stfX_)__ is the prototype implementation of the conceptual framework detailed in chapter 4 of the __corresponding [thesis](docs/thesis.pdf)__.
Thus, the _stfX_ focuses on the quantification of the features of interest occurring over user-inputted spatiotemporal phenomena. The _stfX_ is capable of quantifying a subset of the spatiotemporal change features identified in the literature review, namely: translations, rotations, uniform scaling, changes of direction, moment in time and duration.


The SpatioTemporal Features eXtractor (_stfX_) is a server-side application utilising the microservices architectural approach, thus emphasising self-management, lightweightness, scalability, and autonomy.
In the _stfX_, each microservice represents a component responsible for the quantification of a given category of spatiotemporal change features.
The developed microservices were added to this repository as sub-modules.
Below we can visualise the _stfX_ current architecture:

<img width="702" alt="Screenshot 2020-07-31 at 18 27 18" src="https://user-images.githubusercontent.com/22712373/89060906-b9bedf00-d35b-11ea-92f2-f7dfa2b61a90.png">

The _stfX_'s API can be consulted in the __[corresponding Swagger](https://app.swaggerhub.com/apis-docs/EdgarACarneiro/thesis/2.1.1)__.

Chapter 5 of the __corresponding [thesis](docs/thesis.pdf)__ contains a more detailed description of the _stfX_.

# Functionalities

The current _stfX_ version supports the following functionalities:
- [x] Receives as input both the spatiotemporal dataset modelled as a sequence of temporal snapshots and the thresholds that express the end-user interests;
- [x] Extracts both the shapes and associated timestamps from each temporal snapshot;
- [x] For each temporal snapshot, quantifies the rigid transformations that occurred (translation, uniform scaling and rotation);
- [x] From the identified movement derived features, computes change of direction (the motive behind solely considering the change of direction is explained in section 5.2.2.1 in the __[thesis](docs/thesis.pdf)__);
- [x] Filters the identified spatiotemporal change features according to the user-inputted thresholds;
- [x] Computes, from the identified temporal features, duration and moment in time, even though no thresholds are provided for these features of interest;
- [x] Indexes the identified features of interest by temporal range.

# Repository Folder Overview

| __Folder__ | __Description__ |
|:-|:-|
| `/configs` | Contains necessary configurations for the deployment of the _stfX_. |
| `/core` | [Core microservice](https://github.com/EdgarACarneiro/stfXCore). |
| `/CPD-service` | [CPD microservice](https://github.com/EdgarACarneiro/CPD-service). The CPD is the used point set registration algorithm in this service. Represents the PSR service of the above figure. |
| `/docs` | Contains the associated documents, namely the corresponding __[thesis](docs/thesis.pdf)__. |
| `/PR-GLS-service` | Attempt of developing a microservice employing non-rigid registration. Ended up using the [CPD microservice](https://github.com/EdgarACarneiro/CPD-service). |
| `/swagger` | Contains the `json` file describing the [Swagger](https://app.swaggerhub.com/apis-docs/EdgarACarneiro/thesis/2.1.1) corresponding to the _stfX_. |
| `/utils` | Contains some utility python scripts, such as the parser of `.wkt`to `.json` and extracting the `.json` dataset from an animation modelled as a set of `.obj` files. |
| `/validation` | Contains the resources, the results, and the code necessary to validate the _stfX_. See chapter 6 of the corresponding __[thesis](docs/thesis.pdf)__ for more information. |


# Validation of _stfX_

The purpose of the `/validation` subfolder is none other less than to validate the _stfX_, by containing all the necessary resources to run the experiments and recreate the results documented.

Some important nuances:
1. The validation scripts assume the totality of the stfX environment is running locally, using `docker-compose`.
2. Therefore, please do not forget to run `docker-compose up`, on stfX main folder, before proceeding with the validation.
3. However, to run the experiments that depend on the mock PSR service, you must run the `stfXCore` microservice in a standalone manner (e.g. using _IntelliJ_), and run the mock server available in folder `/validation/psr-mock` (you must not have the original `cpd-service` running, as both server - the mock and the original - run in the same port on localhost). Afterwards, run `sh experiments_with_mock.sh` to run the experiments that support mock data.
4. Before running the validations scripts, remember you have to install the requirements, available on the `/validation`folder, using `pip install -r requirements.txt`. It is suggested to first create a [virtual environment](https://docs.python.org/3/library/venv.html) and activate it.

<details>
<summary><code>utils/</code></summary>
  <br>
  Contains Metrics M1 and M2:
  <ul>
    <li><strong>M1</strong>
      <ul>
        <li>metric that consists of comparing the ground truth final representation with the representation obtained by applying the transformations outputted by the <em>stfX</em>. The goal is to identify if the transformations recognised by the <em>stfX</em> result in equivalent representations to the ground truth.
        </li>
      </ul>
    </li>
    <li><strong>M2</strong>
      <ul>
        <li>metric that consists of analysing the similarity between the set of transformations outputted by the tool and the ground truth transformations. The goal is to identify how similar are both sets of transformations and, therefore, how effective is the devised solution.</li>
      </ul>
    </li>
  </ul> 
</details>

<details>
<summary><code>experiment_mock.sh</code></summary>
  <br>
  This script is in charge of running the mock microservice that runs the algorithm point set registration.
</details>



<details>
  <summary><code>validate.py</code> & <code>validate_all.py</code></summary>
<br>
  <ul>
    <li>
      <code>validate.py</code> Script to validate a test-scenario the stfX tool. Usage:
      <code>

      usage: validate.py [-h] -d DIR [-e ENDPOINT]

      Script to validate a test-scenario the stfX tool.

      optional arguments:
        -h, --help            show this help message and exit
        -d DIR, --dir DIR     The directory containing the resources necessary for this test. The output is also written to
                              this directory, in file result.txt
        -e ENDPOINT, --endpoint ENDPOINT
                              The endpoint running stfX. Default is http://localhost:0080/stfx
  </code>
    </li>
    <li>
      <code>validate_all.py</code> Script to validate all the test-scenario, in the given directory, using the stfX tool. Usage:
      <code>

      usage: validate_all.py [-h] -d DIR [-o OUT_DIR] [-e ENDPOINT]

      Script to validate all the test-scenario, in the given directory, using the stfX tool.

      optional arguments:
        -h, --help            show this help message and exit
        -d DIR, --dir DIR     The directory containing the test-scenarios.
        -o OUT_DIR, --out_dir OUT_DIR
                              The directory where the output of the test-scenario will be written to. If the directory does
                              not exist, it is created. Defaults to './results'
        -e ENDPOINT, --endpoint ENDPOINT
                              The endpoint running stfX. Default is http://localhost:0080/stfx
  </code>
    </li>
  </ul>
</details>

<details>
  <summary><code>results/</code></summary>
  <br>
  Contains the results under the denomination <code><em><name_of_the_test></em>.json</code>. 
  <br><br>
  The json files have both the obtained results, the metric scores, and the differences between what was accomplish and what was expected for the M2 metric.
  <br>
  As for the files under the denomination <code><em><name_of_the_test></em>_m1.png</code>, they exhibit the representation of the <strong>convex hulls</strong> and the phenomena used in the M1 metric.
</details>

<details>
  <summary><code>psr-mock</code></summary>
  <br>
  Contains the imperative code to run the mock microservice.
</details>

<details>
  <summary><code>experiments</code></summary>
  <br>
  Encloses folders for each experiment. As for each experiment, it consists of:
  <ul>
    <li>
      <code>dataset.json</code>: the dataset that characterizes the spatiotemporal phenomena.
    </li>
    <li>
      <code>expected_result.json</code>: the expected result for the M2 metric (changes that effectively affect the phenomenon). 
    </li>
    <li>
      <code><em>name</em>.blend</code>: blender file used to create the experience (phenomenon + geometric transformations applied).
    </li>
    <li>
      <code>thresholds.json</code>: thresholds adopted for the detection of changes that affect the spatiotemporal phenomenon.
      <ul>
        <li>
          Sometimes it happens to exist more than one file for <code>thresholds.json</code> inside an experiment. This occurs because some experiments made with the same dataset adopt different thresholds so that the impact of bad and good thresholds in the quality of the results can be appraised.
        </li>
      </ul>
    </li>
  </ul>
</details>

# Getting started

To download stfX with the latest micro-services versions run:
* As one step:
```shell
git clone --recursive https://github.com/EdgarACarneiro/stfX.git
```
* Two steps - first cloning repository and then updating sub-modules:
```shell
git clone https://github.com/EdgarACarneiro/stfX.git
git submodule update --init --recursive
```

To update the submodules use the command:
```shell
git submodule foreach git pull origin master
```

To add new submodules use the command:
```shell
git submodule add <submudole link>
```

To run the application, using Docker (one must have it installed), run:
```shell
docker-compose up
```

__Important notes to run the application__:
* if you intend to use the `swagger UI`, in the browser, you must change the following lines https://github.com/EdgarACarneiro/stfX/blob/a9fe68d30624d666b440f412a8ead0c5037d2c20/swagger/swagger.json#L13 since these are set for the deployment purposes. Hence, if you are running the `docker-compose up` command locally,
change this lines to:
```json
"url" : "http://localhost:0080/stfx",
```

* By default, the application entrypoint is port `80`, hence you must not have anything running on this port or you must change it in lines https://github.com/EdgarACarneiro/stfX/blob/a9fe68d30624d666b440f412a8ead0c5037d2c20/docker-compose.yml#L9 , for e.g. (serving application on port `82`):
```yaml
- 82:80
```
