# Semi-supervised domain adaptation repository for VisDA 2019 Challenge

## News
2019/09/01 We have released the test data. See data preparation and rule section.

2019/07 We have changed a codalab competition website because the codalab website experienced a major crash.
The new page is here [codalab](https://competitions.codalab.org/competitions/20257).


## Competition Introduction
This repository is for VisDA 2019 Semi-supervised Domain Adaptation track.
Please note that this repo is for validation phase, so you need to modify some part
(especially, data loading part) in test phase training.

## Data preparation (Validation phase)

By downloading these datasets you agree to the following terms:

### Terms of Use
- You will use the data only for non-commercial research and educational purposes.
- You will NOT distribute the images.
- The organizers make no representations or warranties regarding the data, including but not limited to warranties of non-infringement or fitness for a particular purpose.
- You accept full responsibility for your use of the data.

To get data, run

`sh download_data.sh`


The data will be stored in the following way.

`./data/real/category_name`,
`./data/sketch/category_name`

`./data/txt/real_all.txt`,
`./data/txt/sketch_unl.txt`,
`./data/txt/sketch_labeled.txt`,
`./data/txt/sketch_val.txt`,
, where real_all.txt lists all data in the real image domain,
sketch_unl.txt lists unlabeled data in sketch,
sketch_labeled.txt lists labeled examples,
sketch_val.txt lists validation examples.

In the test phase, you will be given the similar txt files.
Of course, target_unl.txt will not include ground truth label in the test phase.


## Data preparation (Test phase)

To get data, run

`sh download_data_test.sh`

You will get 2 domains' image files and corresponding image lists.
Four image lists are given , clipart_labeled.txt, painting_labeled.txt, clipart_unl.txt, painting_unl.txt.
In the test phase, we do not provide validation examples because you can validate your model's performance by submitting to evaluation server.



## Rules (Validation phase)
The domain we will use in validation phase is the following ones.

- Source Domain: Real
- Target Domain: Sketch

In this track, you need to output predictions for all files in target_unl.txt (In the validation phase, target = sketch).
You can use label of examples in source domain and labeled training examples in the target domain.
In addition to the labeled examples, you can use unlabeled target examples in an unsupervised way.
Unsupervised way means that you are not allowed to manually give annotation to the examples.

In addition, labeled validation examples are not allowed to train a model.
They can be used only to validate the performance of the model (We are discussing whether we will provide the valiation examples or not in the test phase.).


## Rules (Test phase)
The domain we will use in testing phase is the following ones.

- Source Domain: Real
- Target Domains: Clipart, Painting

We have two target domains, you need to separately train **two different models**, Real to Clipart adapted model and Real to Painting adapted model.
Using the other domain's data to train a model is not allowed even in an unsupervised way. For example, to train a model for Real to Clipart setting,
the only data you can use is Real and Clipart data.
You need to give predictions to Clipart with clipart-adapted models and Painting with painting-adapted models. In submission, you have to merge the file.
Please see Submission section below.

You can use label of examples in source domain and labeled training examples in the target domain.
In addition to the labeled examples, you can use unlabeled target examples in an unsupervised way.
Unsupervised way means that you are not allowed to manually give annotation to the examples.
Giving pseudo labels are allowed as long as you do not manually give annotation.


**Please note that although we provide data of main domains in multi-source domain adaptation track,
in this track, you are not allowed to use multi-source domains data.
The data you can use as the source domain is what we specify in this semi-supervised DA folder.
(In validation phase, source is "real domain".)**


## Submission (Validation Phase)

The evaluation code above will output a file that can be correctly
evaluated in the [codalab](https://competitions.codalab.org/competitions/20257).
If you use your own code to make a submission file, make sure that
your file has the same number of lines, each line corresponds to
the image file. The sample output file is stored in ./sample/sample_output.txt.

~~Submissions will be evaluated by calculating the classification accuracy of each category and then the mean accuracy across all categories.~~

The ranking will be determined by calculating the classification accuracy of all examples.

## Submission (Test Phase)

The evaluation code above will output a file that can be correctly
evaluated in the [codalab](https://competitions.codalab.org/competitions/20257).

**We decided to change the format of the submission file. Please note this point.**
We provide clipart_unl.txt, painting_unl.txt, and all_list_public.txt.
all_list_public.txt is generated by a following command,

`cat clipart_unl.txt painting_unl.txt > all_list_public.txt`.

So, this file has list of all unlabeled image names.
In submission, you need to provide a file in which each line shows each image file's predicted class.
Each line corresponds to the same line of all_list_public.txt.
The submission file must contain only numbers, which is different from validation phase.
So, we recommend you to output predictions to both domains separately,
then concatenate the file with the command similar to the one above.

The ranking will be determined by calculating the classification accuracy of all examples.

## Evaluation Server and Leaderboards

We are using CodaLab to evaluate results and host the leaderboards for this challenge. You can find the semisupervised image classification competition [here](https://competitions.codalab.org/competitions/20257).


## Submitting to the Evaluation Server

Once the servers become available, you will be able to submit your results:
- Generate "result.txt" following the instruction above.
- Place these files into a zip file named [team_name]_submission
- Submit to the CodaLab evaluation server following the instructions below

To submit your zipped result file to the appropriate VisDA challenge click on the “Participate” tab. Select the phase (validation or testing). Select “Submit / View Results, fill in the required fields and click “Submit”. A pop-up will prompt you to select the results zip file for upload. After the file is uploaded, the evaluation server will begin processing. This might take some time. To view the status of your submission please select “Refresh Status”. If the status of your submission is “Failed” please check your file is named correctly and has the right format. You may refer to the scoring output and error logs for more details.

After you submit your results to the evaluation server, you can control whether your results are publicly posted to the CodaLab leaderboard. To toggle the public visibility of your results please select either “post to leaderboard” or “remove from leaderboard.”


## Baselines and usage
### Install

`pip install -r requirements.txt`

The code is written for Pytorch 0.4.0, but should work for other version
with some modifications.


### Baselines
We provide some baselines in this rep just to help your implementation.

This repo includes baselines "S+T", "ENT", "MME".

- "S+T" is the model trained with source examples and labeled target examples. This model does not use any adaptation method.

- "ENT" is the model trained with unlabeled target examples using entropy minimization in addition to labeled source and target examples

- "MME" is a method for semi-supervised domain adaptation ([arxiv](https://arxiv.org/abs/1904.06487)). This method utilizes domain-adaptive entropy minimization.


### Training

To run training using alexnet,

`sh run_train.sh gpu_id method alexnet`

where, gpu_id = 0,1,2,3...., method=[MME,ENT,S+T].


### Evaluation Script

`sh run_eval.sh gpu_id method alexnet steps`

where, method=[MME,ENT,S+T], steps = which iterations to evaluate.
It will output output.txt. Change the name of this file to upload to codalab.

Calculate the mean accuracies for each category and the overall mean of these accuracies.
We have provided the evaluation script in sample/eval_results.py so that you may evaluate your results offline.
You are encouraged to upload your results to the evaluation server to compare your performance with that of other participants.



###
If you consider using this code or its derivatives, please consider citing:

```
@article{saito2019semi,
  title={Semi-supervised Domain Adaptation via Minimax Entropy},
  author={Saito, Kuniaki and Kim, Donghyun and Sclaroff, Stan and Darrell, Trevor and Saenko, Kate},
  journal={ICCV},
  year={2019}
}
```



### Feedback and Help
If you find any bugs please [open an issue](https://github.com/VisionLearningGroup/visda-2019-public/issues).





