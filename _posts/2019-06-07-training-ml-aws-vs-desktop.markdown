---
layout: post
title:  "Training ML models: AWS vs Desktop?"
date:   2019-06-07 18:45:58 -0500
categories: jekyll update
---
When I started learning Machine Learning I was training very simple models on my laptop, without a GPU. At that point my laptop was enough
for what I wanted to do: learn the very basics. As you can imagine, as soon as those models started becoming a bit more advanced and
having to train with much bigger datasets, using a single CPU wasn't enough.

I started getting frustrated because I wasn't achieving anything running it on my laptop. It would take a really long time to train just for a
few epochs and the results were bad. It was time to decide if I was going to completely stop trying to implement ML models or spend some money
and get a better machine to keep going. I decided to keep going.

At that point the two alternatives that crossed my mind were either using AWS and spend money only on whatever resources I use or find a good
list of components and put an affordable but powerful PC together. To make the decision, I made some initial assumptions about the future projects
I would implement, and made some price calculations.

These assumptions about my projects were the following (I explain later why these are incorrect):

  - I would be working on around 10 projects total in the next couple of years (I work on them on my free time and that is all I thought I would have time for)
  - Each project could take me around 12 complete days to train them, including some retraining due to errors that I could encounter
  - I could do some prototyping on my laptop and only start training with a GPU once I see everything is looking ok
  - An 8 GB and around 2000 core GPU would be good enough

With these things in mind, I started estimating prices.

<br/>
## Estimating AWS price:

The cheapest AWS instance type with GPU at the time of this writing is the g3s.xlarge type with a price of 0.75 dollars per hour. Here are the specs:

| Instance type | vCPUs | Memory | GPUs | GPU model | GPU memory | GPU cores | Price
|-------|--------|---------|---------|---------|---------|---------|---------|
| g3s.xlarge | 4 | 30.5 | 1 | Tesla M60 | 8 GB | 2048 | $0.75 |

On top of that, I would have to pay for S3 storage for the datasets and S3 transfer:

  - 0.023 per GB for the first 50 TB (I wasn't planning even to get to 1 TB)
  - Data transfers from S3 to EC2 and vice versa are for free
  - Data from outside AWS into S3 is free
  - Data from S3 to outside AWS is 0.09 per GB
  - S3 operations (PUT, COPY, POST, LIST, GET) have a small cost

Finally I would have to add a bit more for SSD storage and taxes (yes, don't forget about this when estimating AWS costs).

With this information, the total estimated cost of running my projects on AWS following the assumptions that I mentioned above
was **around 2300 dollars**.

<br/>
## Estimating Desktop price:

For a quick estimation of the cost of building my own PC I followed [this article](https://medium.com/the-mission/how-to-build-the-perfect-deep-learning-computer-and-save-thousands-of-dollars-9ec3b2eb4ce2).
The cheaper desktop recommended is **2000 dollars**. To be completely fair, we would also have to add the extra price for electricity when letting it
run for several days in a row.

<br/>
## Making a decision:

Since the prices that I estimated were kind of similar I decided to start with AWS. More specifically I was going to use the **g3s.xlarge instance
type with the Deep Learning Base AMI (Amazon Linux 2) Version 18.0 (ami-05603f16d12d9996f)** to train a fast rcnn object detector and classifier that I
was working on.

I let it run for a couple of days and to be honest, the results weren't good. Everything seemed ok when doing prototyping on my laptop but the model
wasn't learning much. I spent 2 days to get pretty bad results and had to start doing some thorough testing and analysis. I only had 10 more days according
to my initial assumptions.

This helped me realize that **maybe my assumptions weren't completely correct**. Just 12 days didn't seem enough to train a model anymore. I want to be able to
train the same model in many different ways to see how I can get better precision and be able to run many different tests when things are not looking good.

At the same time I was starting to get paranoid about how much money I was spending on AWS and would want to run things for as short of a period of time
as possible, to be able to terminate the EC2 instances and remove data from S3. I wasn't able to work properly this way!

At that point I made up my mind and **decided to start looking into putting together my own desktop machine**.

<br/>
## Building an affordable ML desktop machine:

I used the following very useful links to learn more about how to pick components for a desktop that is going to be used mainly for ML projects (although
potentially for some other type of projects):

- [Why building your own Deep Learning Computer is 10x cheaper than AWS](https://medium.com/the-mission/why-building-your-own-deep-learning-computer-is-10x-cheaper-than-aws-b1c91b55ce8c)
- [How to build the perfect Deep Learning Computer and save thousands of dollars](https://medium.com/the-mission/how-to-build-the-perfect-deep-learning-computer-and-save-thousands-of-dollars-9ec3b2eb4ce2)
- [A Full Hardware Guide to Deep Learning](https://timdettmers.com/2018/12/16/deep-learning-hardware-guide/)

Of course I also used the well known [pcpartpicker web](https://pcpartpicker.com) to find possible incompatibilities between components, calculate the final wattage of the system and
to know where to buy the components from to get the cheapest price.

Some important details about the components are the following:

  - **GPU**
    * Here is where you want to put your money since training ML models specially benefits from GPUs.
    * GPU memory: 8 GB should be good enough for personal projects (for example Kaggle projects).
    * Get an Nvidia GPU since it is required by some frameworks to be able to work with your GPU (like Tensorflow).
    * RTX and GTX give the best quality / price balance. RTX come with Tensor Cores which are more specialized than CUDA cores (more info [here](https://devblogs.nvidia.com/programming-tensor-cores-cuda-9/)).
<br/><br/>
  - **Motherboard**
    * Make sure it has enough PCIe 16x for the number of GPUs you want your system to have.
    * It is nice to have M.2 ports for SSD disks.
    * Make sure you pick the right motherboard for your CPU! Pcpartpicker should tell you this.
<br/><br/>
  - **CPU**
    * You will probably want to process big datasets in parallel prior to training the models, so check the number of cores offered by the CPU you want.
    * I picked 8 cores with a total of 16 threads, but fewer cores should also be good enough.
    * Thermal paste for the CPU fan should come with the CPU already but make sure that is the case.
<br/><br/>
  - **Memory**
    * Use DDR4 for good memory speed.
    * Data is going to be stored in memory before making it to the GPU. Also your data pre-processing might be memory intensive.
    * 32 GB was good enough for me.
<br/><br/>
  - **Power supply**
    * Make sure it can support the wattage your system will consume.
    * Your GPU will consume between 185 and 250 watts.
    * Ryzen 7 CPUs are between 65 and 105 watts and Ryzen Threadripper CPUs between 180 and 250 watts.
    * Get more wattage than the one your system will consume so you can add more components in the future if needed.
<br/><br/>
  - **Storage**
    * Get an SSD drive with M.2 connector.
    * Make sure you get enough space to fit your datasets.
<br/><br/>
  - **Case**
    * Get a case that can provide good cooling.
    * Each GPU requires 2 expansion slots, so make sure it can fit the number of GPUs you want to install.

For more details about how to pick the different components, check the links that I mentioned above.

After doing some research about the components, **I noticed that I could get a good desktop for a much cheaper price than my initial estimate of
2000 dollars**. This made it even more obvious that going with my own machine was way better than using AWS in my case. These are the components I got:

<br/>
![Partpicker desktop components]({{ site.baseurl }}/assets/2019-06-07-partpicker-building-pc-for-ml.png)
<br/><br/>

Notice that I picked a motherboard that can only support 3 GPUs, as opposed to what some of the articles above recommend to maximize
the savings compared to AWS, which is 4 GPUs. To be honest I don't think I'll ever have more than 2 GPUs running together.

Also I decided to go with AMD Ryzen 7 instead of Threadripper because the Ryzen 7 gives me enough cores and I can pick a cheap montherboard. The
price of the ASUS motherboard plus the Ryzen 7 CPU was very similar to just the motherboard for the Threadripper CPU.

This was the first time that I was putting a computer together, so I decided to first watch some videos that show you how to do it in a very detailed manner. I
recommend the following video:

<br/>
<iframe width="560" height="315" src="https://www.youtube.com/embed/VIF43-0mDk4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<br/>

Now it is time to install the operating system (I'll go with the latest Ubuntu LTS) and run some component stress tests to make sure the system is stable.