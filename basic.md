
###### This documentation was written for Charmed Kubeflow based on Kubeflow 1.1 and Katib v1alpha3.

## Using Kubeflow - Basic

Congratulations on installing Kubeflow! This page will run through setting up a typical workflow to familiarise you with how Kubeflow works. 

These instructions assume that :

-  You have already [installed Kubeflow][link] on your cluster (full or lite version).
-  You have [logged in to the Kubeflow dashboard][link]

This documentation will go through a typical, basic workflow so you can familiarise yourself with Kubeflow. Here you will find out how to:

-  Create a new Jupyter notebook
-  Add a Tensorflow example image
-  Create a pipeline
-  Start a run and examine the result

## Get to know the Dashboard

![dashboard image][dashboard-img]

The Kubeflow Dashboard combines some quick links to the UI for various components of your Kubeflow deploy (Notebooks, Pipelines, Katib) as well as shortcuts to recent actions and some handy links to the upstream Kubeflow documentation. 

## Notebook Servers

This Dashboard will give you an overview of the Notebook Servers currently available on your Kubeflow installation. In a freshly installed Kubeflow there will be no Notebook Server.

You cancreate a new Notebook Server by clicking on `Notebook Servers` in the left-side navigation and then clicking on the `New Server` button.

![Notebook server image][notebook-new-img]

In the New Server section you will be able to specify several options for the New Notebook Server that you are creating. In the image section choose an image of tensorflow 2, it is required for our example notebook.

![alt_text](images/image3.png "image_tooltip")

Once the Notebook Server is created you will be able to connect to it and access your Jupyter Notebook environment which will be opened in a new tab.

![alt_text](images/image4.png "image_tooltip")

For testing the server we will upload the[ Tensorflow 2 quickstar for experts example](https://www.tensorflow.org/tutorials/quickstart/advanced) using the upload buttons and uploading the already downloaded notebook.

![alt_text](images/image5.png "image_tooltip")

Once uploaded we click on the notebook name, which will open a new tab with the notebook content and the tooling for us to execute it.

![alt_text](images/image6.png "image_tooltip")

Now we will be able to execute through the notebook using the Run button.

## Pipelines

For developing a pipeline the [official Kubeflow Documentation][] is up-to-date and recommended.

For uploading your first pipeline you can use one of the[ kubeflow testing pipelines](https://github.com/juju-solutions/bundle-kubeflow/blob/master/tests/pipelines/mnist.py).

For building it execute the following commands in a terminal:

```
git clone https://github.com/juju-solutions/bundle-kubeflow.git
cd bundle-kubeflow
python3 -m venv pipelines
source pipelines/bin/activate
pip install kfp
cd tests/pipelines
dsl-compile --py mnist.py  --output mnist.yaml
```

Once compiled go to the Kubeflow Pipelines Dashboard and click on the Upload pipeline button.

![alt_text](images/image7.png "image_tooltip")

In the upload section choose the “Upload a file” section and choose the mnist.yaml file generated with the previously executed command. Afterwards click “Create” which will create the Pipeline in our Kubeflow Dashboard.

![alt_text](images/image8.png "image_tooltip")

Once the pipeline is created we will be redirected to its Dashboard. Here we will click on Create run to create our first Pipeline Run.

![alt_text](images/image9.png "image_tooltip")

For this run we can leave all the default parameters and options as this is a test run and will only be a One-off run. Therefore we will proceed to click “Start”

![alt_text](images/image10.png "image_tooltip")

Once the run is started, it will redirect us to the run dashboard where we will go through all the stages of the run. After some minutes there should be a checkpoint in each pipeline component showing us that the run has been executed successfully.

![alt_text](images/image11.png "image_tooltip")

## Katib 

Katib will offer you *explain Katib*

You can execute Experiments from the UI and from CLI we will show you both examples:

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

![alt_text](images/image12.png "image_tooltip")

Afterwards we will copy paste the content of this[ link](https://raw.githubusercontent.com/kubeflow/katib/4559e16/examples/v1alpha3/grid-example.yaml) into the Text field. Make sure to change the namespace field in the metadata section to the namespace where you want to deploy your experiment. Afterwards we will click _Deploy_.

![alt_text](images/image13.png "image_tooltip")

Once the Experiment has been submitted we will go to the Katib Menu and select _Monitor_ in the _HP_ section.

![alt_text](images/image14.png "image_tooltip")

In the Monitor section, we will select our grid-example.

![alt_text](images/image15.png "image_tooltip")

In the section of our grid-example Experiment we will be able to view our Trials and check the Experiment and Suggestion outputs.

![alt_text](images/image16.png "image_tooltip")

### Finish

<!-- IMAGES -->

[dashboard-img]: https://assets.ubuntu.com/v1/48a61b3f-kubeflow-dashboard.png "Kubeflow Dashboard v1.1" 
[notebook-new-img]: images/image1.png "image_tooltip"
<!-- LINKS -->
[official Kubeflow Documentation]: https://www.kubeflow.org/docs/pipelines/ "kubeflow docs"