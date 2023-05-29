
<p align="center" width="100%">
<a href="https://self-feedback-beta.dreamgonf.ly/" target="_blank"><img src="assets/llama_selfie.png" alt="KAIST-Selfee" style="width: 50%; min-width: 300px; display: block; margin: auto;"></a>
</p>

# SelFee: Iterative Self-Revising LLM Empowered by Self-Feedback Generation

[![Code License](https://img.shields.io/badge/Code%20License-Apache_2.0-green.svg)](https://github.com/tatsu-lab/stanford_alpaca/blob/main/LICENSE)
[![Data License](https://img.shields.io/badge/Data%20License-CC%20By%20NC%204.0-red.svg)](https://github.com/tatsu-lab/stanford_alpaca/blob/main/DATA_LICENSE)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/release/python-390/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)


## News
[May 31, 2023] Initial release: We released the first version of Selfee! Check out the <a href="https://lklab.kaist.ac.kr/">blog post</a> for more details.


## Overview
This is the repository for the KAIST Selfee project, which aims to build and share an instruction-following LLaMA model. This repo mainly has five contents:
- The selection process of the 178K training data for Selfee ([detail](#data-release), [code](data_collection)).
- The generation process for the training data and its result. ([detail](#data-generation-process), [code](data_augmentation)).
- The training process for the model  ([detail](#training), [code](train)).
- The inference process for the model ([detail](#inferencing), [code](inference)).
- The evaluation method and dataset ([detail](#evaluating), [code](evaluation)).


<img width="565" alt="selfee_workflow" src="assets/selfee_workflow.png">


This repository is based on the [Stanford-Alpaca](https://github.com/tatsu-lab/stanford_alpaca/) and [Vicuna](https://github.com/lm-sys/FastChat/) repository. Thanks to all the contributors for these awesome repositories!! 🙌



**We highly recommend you read our [blog post](https://lklab.kaist.ac.kr/) for more details about the model.**


## Data Release
For data collection, we collected datasets from five different fields. These are the Stanford Alpaca dataset, math collection, code collection, Flan collection, and ShareGPT.
<b>Stanford Alpaca dataset</b>
<p>
	We include the dataset used to train the Stanford Alpaca model. The dataset contains around 52k examples, which is generated by OpenAI’s text-davinci-003 following the self-instruct process.<br>
</p>
<b>Math collection</b>
<p>
	The math collection included three kinds of datasets: Aqua (10k), GSM8K (8.79k), and Math (7.47k). Since we knew the answers to these questions, we asked ChatGPT for feedback after providing the correct answer.<br>
</p>	
<div class="entry-title title-sm">
	<b>Code collection</b>
</div>
<p>
	The code collection had three types of datasets, namely Conala (2.78k), Deepmind Code Contest (8.16k), Dr Repair (18k), and Mbpp (969). As with the math collection, we asked ChatGPT for feedback after providing the correct answer.<br>
</p>	
<div class="entry-title title-sm">
	<b>Flan collection</b>
</div>
<p>
	The Flan collection included subsets of Flan collection datasets such as Flan (Flan 2021), T0 (P3 excluding Flan 2021), and Niv2 (Super-Natural Instructions). We only used ten instances per task and excluded the subsets from Dr Repair and Deepmind Code Contest because they were included in the code collection. As with the math and code collections, we asked ChatGPT for feedback after providing the correct answer. Our collection had 15.6k instances.<br>
</p>	

<div class="entry-title title-sm">
	<b>ShareGPT</b>
</div>
<p>
	We initially collected 90k dialogues shared by users on ShareGPT were collected using public APIs.To maintain data quality, we removed any non-English conversations and deduplicated at the user-query level. We utilized only the first instruction-answer pair from human and machine and removed any machine answer ending with a question mark, as we only required descriptive instructions. This resulted in 54.6k examples for training.<br>
</p>		

## Data Generation Process

<source media="(min-width: 1200px)" src="videos/Selfee_Data_Augmentation_Scheme.mp4" type="video/mp4">
To train our model with high-quality instructions and answer pairs, we utilized data augmentation using OpenAI API calls. The process involved three steps. <br>
Firstly, we collected various instructions from multiple fields and fed them to ChatGPT to generate answers. <br>
Secondly, we gathered feedback on the generated answer by querying ChatGPT again and asked it to determine if the initial answer required any revision. <br>
Thirdly, if a revision was necessary, we passed the instruction, initial answer, and feedback pair to ChatGPT to generate a revised answer and its feedback pair. 
We repeated the process until we received feedback that required no further revision or hit the maximum iteration. However, due to the token limitation of the ChatGPT API, we had to truncate some instances that needed more than 4096 tokens while augmenting.<br>


## Training
We utilize <a href="https://github.com/lm-sys/FastChat">FastChat</a> to train the model. Given the instruction, we fine-tune the model to generate the whole answer and feedback chain.<br>

## Inferencing
<b>Experiment of enforcing revision</b><br>
One thing that we noticed during inferencing was that the qualiy of an answer got higher as there were more revisions. When we augmentated the training data through ChatGPT, more revision did not always guarantee higher-quality answer. Especially, forcing to revise an output that ChatGPT thinks that revision is not needed dropped the quality of the output. <br>
Selfee, on the other hands, was trained to autoregressively revise the output with feedback. So we were curious whether enforcing our model to revise any question would help enhancing the quality of output, and here's the result!<br>

## Evaluating
<b>GPT-4 assessment</b><br>
<b>Self consistency evaluation</b><br>
We noticed that the qualiy of an answer got higher as there were more revisions. So we enforced our model to revise at least twice, see the outcomes!<br>

### Online demo
Check out the <a href="https://self-feedback-beta.dreamgonf.ly/">demo</a>!


### Team members
<a href="mailto:seonghyeon.ye@kaist.ac.kr">Seonghyeon Ye</a>,  <a href="mailto:yongrae@kaist.ac.kr">Yongrae Jo</a>, <a href="mailto:doyoungkim@kaist.ac.kr">Doyoung Kim</a>, <a href="mailto:sungdong.kim@kaist.ac.kr">Sungdong Kim</a>, <a href="mailto:hbin0701@kaist.ac.kr">Hyeonbin Hwang</a>, and <a href="mailto:minjoon@kaist.ac.kr">Minjoon Seo</a>.

### Release
We will make available the training, serving, and evaluation code for our first release on a GitHub repository: <a href="https://github.com/seonghyeonye/Self-Feedback">https://github.com/seonghyeonye/Self-Feedback</a>. Additionally, we have released the Selfee-7B and Selfee-13B model weights, which can be found with instructions <a href="https://github.com/seonghyeonye/Self-Feedback#selfee-weights">here</a>. Keep up-to-date with the latest developments by following us on <a href="https://twitter.com/">Twitter</a>!

### License

The research preview online demo is only for non-commercial use and is subject to various licenses and terms of use, including the LLaMA model <a href="https://github.com/facebookresearch/llama/blob/main/MODEL_CARD.md">License</a>, OpenAI's <a href="https://openai.com/policies/terms-of-use">Terms of Use</a> for the generated data, and ShareGPT's <a href="https://chrome.google.com/webstore/detail/sharegpt-share-your-chatg/daiacboceoaocpibfodeljbdfacokfjb">Privacy Practices</a>. If you suspect any violations, please reach out to us.





### Citation

Please cite if you use the data or code in this repo.

```
@misc{selfee2023,
title = {Selfee: Chatbot that revises its own answers beats ChatGPT with 7B scale},
url = {https://lklab.kaist.ac.kr/},
author = {Ye, Seonghyeon$^*$ and Jo, Yongrae$^*$ and Kim, Doyoung$^*$ and Kim, Sungdong and Hwang, Hyeonbin and Seo, Minjoon},
month = {May},
year = {2023},
note = {$^*$ indicates equal contribution}
}
```
