# Action Chunking Transformer (refactoring of training and arch creation)

This repository contains a refactor of [the Action Chunking Transformer](https://github.com/tonyzhaozh/act) by Tony Zhao et al (paper: [Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware](https://arxiv.org/abs/2304.13705)).

I made a couple of simplifications along the way, mostly on the inference side of things (for instance, I removed temporal ensembling). 

The architecture and training should be unchanged.

To run the training code, you need [the DETR code from facebook research](https://github.com/facebookresearch/detr).

In order to roll out the trained policy in `dmcontrol` (`mujoco`) you need to install a couple of things as outlined in [the original action transformer repo](https://github.com/tonyzhaozh/act). You also need the [ACT code](https://github.com/tonyzhaozh/act) itself as this notebook doesn't include `make_sim_env` and `BOX_POSE` (training should work regardless, we only depend on `DETR` for it)

This code doesn't contain any original ideas from me, it is simply a refactor of the outstanding research shared by Tony Zhao et al.

I would like to thank Tony Zhao and his colleagues for sharing their work -- there are many conventions and ideas that are completely opaque to people from outside the field of robotics. For instance, for the longest time I didn't even how the control of the robot is achieved! Turns out the solution is extremely simple -- states become movement targets. And that is the tip of the iceberg of the puzzles that are missing for people from outside the field that the insiders take for granted :).

So without actual code being shared, reading papers is absolutely hopeless. You need some anchor point to get you inside. The Action Chunking Transformer, on top of being an extremely important piece of cutting-edge research, provides the link, for which I feel we all should be very grateful.

As for the setup -- I opted to leave the data loading code (nearly) unchanged. Another extremely important piece of the shared ACT codebase is simulation. Creating training episodes in `dmcontrol`, rolling out the trained policy, etc. I played around with it to understand what it does, but I decided to invest my time on the architecture/training side of things.

Having said that, it would be extremely valuable to explore how these simulations are created, how to create new environments, how to set rewards, etc. Might be a good idea for another refactor/blog post, or maybe someone else would like to give it a go? Being able to import architectures from STEP files for various robot arms sounds like a super valuable functionality. Especially that, unfortunately, validation loss is not a perfect predictor of performance on the metric, so the only way to tell whether your model has improved is via rollout validation. And here, the metric is noisy as well, so ideally you want a lot of rollouts. And that in the real world gets tedious, though it can be automated to some extent as well.

Without further ado, here is the code. `DETR` was also seminal, interesting, and very welcome (calculating anchors and nms-ing predictions was fun in its own painful way, but I do welcome any methods that can safeguard us from this going forward!), and in particular DETR was a novel way of leveraging the transformer architecture.

Awesome here we get to explore both!

Just one last side note -- you can download the training data using google drive links in the README of [ACT](https://github.com/tonyzhaozh/act), but it is a major pain as you cannot really do it via the web gui. I used [rclone](https://rclone.org/drive/) and set up an application in GCS and the transfer itself cost me a couple of dollars. There is a way of downloading using rclone without using a paid gcs account, but I haven't tried that.

Alternatively, you can generate your training episodes using `record_sim_episodes.py` in [the ACT repo](https://github.com/tonyzhaozh/act) which IMO is the way to get started though you will only have scripted data and not the episodes recorded by humans (human-sourced data is more challenging due to the inherent noisiness/multiple ways of going about the same thing).
