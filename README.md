# ML CI/CD Pipeline with Gatekeeper Logic

A GitHub Actions pipeline that treats GPU training as an expensive resource to be gated, not a step that runs on every push.

Part of a 3-stage progression exploring ML CI/CD design: [mlops-pipeline](https://github.com/mohamedtaha77/mlops-pipeline) (basic training + MLflow logging) to [Full-CI-CD-pipeline](https://github.com/mohamedtaha77/Full-CI-CD-pipeline) (Docker + accuracy threshold) to this one, which adds explicit gatekeeper controls around when training runs at all.

## How the gate works

```
push (any branch)
      │
      ▼
   lint job (flake8)
      │
      ▼ (only if lint passes)
   train job, but only runs when ALL of:
     - branch is main
     - commit message contains "[run-train]"
      │
      ▼
   train.py runs on "GPU"
      │
      ▼ (on failure)
   error log captured and uploaded as a build artifact
```

The training job normally does not run at all. Pushing to a feature branch, or pushing to `main` without the `[run-train]` tag in the commit message, stops at the lint step. That is the core idea: keep the expensive job behind an explicit, auditable trigger instead of firing it on every commit.

## Why gate it

GPU jobs cost money and time. A typical CI setup runs training on every push, which is fine for a five-minute job and wasteful for anything longer. This pipeline requires a human to opt in per commit (`[run-train]`) and only on `main`, so accidental or exploratory pushes never trigger a training run.

## What's stubbed

`train.py` is a placeholder, since the point of this repo is the pipeline logic around training, not the model itself. Swapping in a real training script does not change the gate.

## Stack

GitHub Actions, flake8, Python
