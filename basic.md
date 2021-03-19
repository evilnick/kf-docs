
###### This documentation was written for Charmed Kubeflow based on Kubeflow 1.1 and Katib v1alpha3.

## Using Kubeflow - Basic

Congratulations on installing Kubeflow! This page will run through setting up a typical workflow to familiarise you with how Kubeflow works. 

These instructions assume that :

-  You have already [installed Kubeflow][link] on your cluster (full or lite version).
-  You have [logged in to the Kubeflow dashboard][link].
-  You have access to the internet for downloading the example code (notebboks, pipelines) required.
-  You can run Python 3 code in a local terminal (required for building the pipeline).

This documentation will go through a typical, basic workflow so you can familiarise yourself with Kubeflow. Here you will find out how to:

-  Create a new Jupyter notebook
-  Add a Tensorflow example image
-  Create a pipeline
-  Start a run and examine the result


Before you start:

1. Download the tensorflow example notebook from the [Tensorflow 2 quickstart for experts tutorial][tf-tutorial].
   Click on the link aobove and click on the `Download Notebook` button just below the heading. This will download the file `advanced.ipynb` into your usual Download location. This file will be used to create the example notebook.
  
## Get to know the Dashboard

![dashboard image][dashboard-img]

The Kubeflow Dashboard combines some quick links to the UI for various components of your Kubeflow deploy (Notebooks, Pipelines, Katib) as well as shortcuts to recent actions and some handy links to the upstream Kubeflow documentation. 

## Creating a Kubeflow Notebook   

This Dashboard will give you an overview of the Notebook Servers currently available on your Kubeflow installation. In a freshly installed Kubeflow there will be no Notebook Server.

You can create a new Notebook Server by clicking on `Notebook Servers` in the left-side navigation and then clicking on the `New Server` button.
In the New Server section you will be able to specify several options for the New Notebook Server that you are creating. In the image section choose an image of tensorflow 2, it is required for our example notebook.

![Notebook image][notebook-tensorflow-img]

Once the Notebook Server is created you will be able to connect to it and access your Jupyter Notebook environment which will be opened in a new tab.

![Notebook image][notebook-open-img]

For testing the server we will upload the [Tensorflow 2 quickstart for experts example](https://www.tensorflow.org/tutorials/quickstart/advanced).
Click on the link above and click on the `Download Notebook` button just below the heading. This will download the file `advanced.ipynb` into your usual Download location. This file will be used to create the example notebook.

On the `Notebook Server` page, click on the `Upload` button and select the `advanced.ipnyb` file.

![Notebook image][notebook-upload-img]

Once uploaded, click on the notebook name to open a new tab with the notebook content.

![Notebook image][notebook-content-img]

You can read through the content for a better understanding of what this notbook does. Click on the Run button to execute each stage of the document, or click on the double-chevron (`>>`) to execute the entire document. 

## Adding Kubeflow Pipelines

The [official Kubeflow Documentation][] explains the recommended workflow for creating a pipeline. This documentation is well worth reading thoroughly to understand how pipelines are constructed. For this example run-through though, we can take a shortcut and use one of the [Kubeflow testing pipelines][kubeflow-testing-pipeline-source].

If you wish to skip the step of building this pipeline yourself, you can instead [download the compiled YAML file][mnist-yaml]. 

To install the pipeline compiler tools, you will need to first have Python 3 avaiable, and whichever [pip][] install tool is relevant for your OS. On Ubuntu 20.04 and similar systems:

```bash
sudo apt update
sudo apt install python3-pip
```
Next, use `pip` to install the Kubeflow Pipeline package

```bash
pip3 install kfp
```

(depending on your operating system, you may need to use `pip` instead of `pip3` here, but make sure the package is installed for Python3)

Next fetch the Kubeflow repository:

```
git clone https://github.com/juju-solutions/bundle-kubeflow.git
```

The example pipelines are Python files, but to be used through the dashboard, they need to be compiled into a YAML. The `dsl-compile` command can be used for this usually, but for code which is part of a larger package, this is not always straightforward. A reliable way to compile such files is to execute them as a python module in interactive mode, then use the kfp tools within Python to compile the file.

First, change to the right directory:

```bash
cd bundle-kubeflow/tests
```

Then execute the pipelines/mnist.py file as a module:

```bash
python3 -i -m pipelines.mnist
```

With the terminal now in interactive mode, we can import the `kfp` module:

```python
import kfp
```
... and execute the function to compile the YAML file:

```python
kfp.compiler.Compiler().compile(mnist_pipeline, 'mnist.yaml')
``` 

In this case, `mnist_pipeline` is the name of the main pipeline function in the code, and `mnist.yaml` is the file we want to generate.

Once you have the compiled YAML file (or downloaded it from the link above) go to the Kubeflow Pipelines Dashboard and click on the `Upload Pipeline` button.
In the upload section choose the “Upload a file” section and choose the mnist.yaml file. Then click “Create” to create the pipeline.

![pipeline upload image][pipeline-upload-img]

Once the pipeline is created we will be redirected to its Dashboard. Click on `Create Run` to create your first Pipeline run!

![pipeline image][pipeline-graph-img]

For this test run select 'One-off' run and leave all the default parameters and options. Then click `Start`. 
Once the run is started, the browser will redirect to the `run dashboard`, detailing all the stages of the run. After a few minutes there should be a checkpoint showing that the run has been executed successfully.

![pipeline image][pipeline-run-img]

## Running experiments with Katib

If you are unfamiliar with Katib and hyperparameter tuning, plenty of information is available on the upstream [Kubeflow documentation][]. In summary, Katib automates the tuning of machine learning `hyperparameters` - those which control the way and rate at which the AI learns. By running experiments, Katib can be used to get the most effective hyperparameter values for the current task.
Each experiment rpresents a single tuning operation and consists of an objective (what is to be optimised), a search space(the constraints used for the optimisation) and an algorithm(how to find the optimal values).

You can run Katib Experiments from the UI and from CLI.

For CLI execute the following commands:

```
curl https://raw.githubusercontent.com/kubeflow/katib/4559e16/examples/v1alpha3/grid-example.yaml > grid-example.yaml
kubectl apply -f grid-example.yaml
```

If you are using a different namespace than `kubeflow` make sure to change that in `grid-example.yaml` before applying the manifest.

These commands will  download an example which will create a katib experiment which we will be able to inspect its execution the following way:

```
kubectl -n kubeflow get experiment grid-example -o yaml
```

If we want to use the UI we can use the same example. We will go to the Katib Dashboard and select _Hyperparameter Tuning_.

![katib image][katib-img-01]

Afterwards we will copy paste the content of this[ link](https://raw.githubusercontent.com/kubeflow/katib/4559e16/examples/v1alpha3/grid-example.yaml) into the Text field. Make sure to change the namespace field in the metadata section to the namespace where you want to deploy your experiment. Afterwards we will click `Deploy`.


![katib image][katib-img-02]

Once the Experiment has been submitted we will go to the Katib Menu and select `Monitor` in the `HP` section.


![katib image][katib-img-03]

In the Monitor section, we will select our grid-example.

![katib image][katib-img-04]

In the section of our grid-example Experiment we will be able to view our Trials and check the Experiment and Suggestion outputs.


![katib image][katib-img-05]

### Next steps

This has been a very brief run through of the major components of Kubeflow. To discover more about Kubeflow and its components, we recommend the following resources:

-  The upstream [Kubeflow Documentation][official Kubeflow Documentation].


<!-- IMAGES -->

[dashboard-img]: https://assets.ubuntu.com/v1/48a61b3f-kubeflow-dashboard.png "Kubeflow Dashboard v1.1" 
[notebook-new-img]: images/image1.png "image_tooltip"
[notebook-upload-img]: https://assets.ubuntu.com/v1/db3d455a-kubeflow-jupyter-upload.png "uploading a Jupyter notebook"
[notebook-tensorflow-img]: https://assets.ubuntu.com/v1/bf43a088-kubeflow-tensorflow2.png
[notebook-content-img]: https://assets.ubuntu.com/v1/03f22365-kubeflow-tensorflow-quickstart.png
[pipeline-upload-img]: https://assets.ubuntu.com/v1/f571efdf-kubeflow-upload-pipeline.png
[pipeline-run-img]: https://assets.ubuntu.com/v1/72ffcc9e-kubeflow-pipeline-run.png
[pipeline-graph-img]: https://assets.ubuntu.com/v1/266408eb-kubeflow-pipeline-graph.png
[kubeflow-img-01]: https://assets.ubuntu.com/v1/087c7574-kubeflow-custom-image.png
[notebook-new-img]: https://assets.ubuntu.com/v1/b9481023-kubeflow-jupyter.png
[kubeflow-img-03]: https://assets.ubuntu.com/v1/bf43a088-kubeflow-tensorflow2.png
[kubeflow-img-04]: https://assets.ubuntu.com/v1/75e66e12-kubeflow-tensorflow2-quickstart.png
[notebook-open-img]: https://assets.ubuntu.com/v1/e96568aa-kubeflow-tensorflow-notebook.png
[katib-img-01]: https://assets.ubuntu.com/v1/43897674-Screenshot+from+2021-03-19+14-03-59.png
[katib-img-02]: https://assets.ubuntu.com/v1/43897674-Screenshot+from+2021-03-19+14-03-59.png
[katib-img-03]: https://assets.ubuntu.com/v1/43897674-Screenshot+from+2021-03-19+14-03-59.png
[katib-img-04]: https://assets.ubuntu.com/v1/43897674-Screenshot+from+2021-03-19+14-03-59.png
[katib-img-05]: https://assets.ubuntu.com/v1/43897674-Screenshot+from+2021-03-19+14-03-59.png

<!-- LINKS -->
[official Kubeflow Documentation]: https://www.kubeflow.org/docs/pipelines/ "kubeflow docs"
[tf-tutorial]: https://www.tensorflow.org/tutorials/quickstart/advanced "tensorflow tutorial"
[kubeflow-testing-pipeline-source]: https://github.com/canonical/bundle-kubeflow/blob/master/tests/pipelines/mnist.py
[mnist]: http://yann.lecun.com/exdb/mnist/
[pip]: https://pypi.org/project/pip/
[mnist-yaml]: https://raw.githubusercontent.com/canonical/bundle-kubeflow/master/docs/examples/mnist.yaml
[kfp-docs]: https://kubeflow-pipelines.readthedocs.io/en/latest/source/kfp.html
[Kubeflow documentation]: https://www.kubeflow.org/docs/components/katib/overview/



