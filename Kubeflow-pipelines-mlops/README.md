# Psitron Kubeflow pipelines 

# Kubeflow Pipelines: How to Build your First Kubeflow Pipeline from Scratch
This repository aims to develop a step-by-step tutorial on how to build a Kubeflow Pipeline from scratch in your local machine.

If you want to know in detail about the detailed explanation of how to develop your first kubeflow pipeline, I recommend you take a look at the article: <a href="Kubeflow Pipelines: How to Build your First Kubeflow Pipeline from Scratch"> *Kubeflow Pipelines: How to Build your First Kubeflow Pipeline from Scratch*</a>

<p align="center">
<img src='img/kubeflow.jpg'>
</p>

<!-- files -->
## Files
* **decision_tree**: Contains the files to build the decision_tree component as well as the Dockerfile used to generate the component image.
* **logistic_regression**: Contains the files to build the logistic_regression component as well as the Dockerfile used to generate the component image.
* **download_data**: Contains the files to build the download_data component as well as the Dockerfile used to generate the component image.
* **pipeline.py**: Contains the definition of the pipeline, which when executed generates the ``FirstPipeline.yaml`` file.


<!-- how-to-use -->
## How to use
* It is recommended to have previously installed ``kfp`` as well as configured kubeflow on top of kubernets or a minimal version such as ``minikube`` or AKS or EKS.

<!-- license -->
## License
Distributed under the MIT License. See ``LICENSE.md`` for more information.

