# Hugging Face 🤗 x OpenDILab/DI-engine

[English](./README.md) | 简体中文

> Huggingface_ding 代码库用于提供 Huggingface Hub 的 API 封装，用于快速拉取 OpenDILab/DI-engine 的公开模型，或是用于将使用 OpenDILab/DI-engine 训练的模型推送至 Huggingface Hub.

<!-- toc -->

- [安装方法](#安装方法)
- [案例](#案例)
  - [下载模型](#下载模型)
  - [上传模型](#上传模型)

# 安装方法
## 使用 pip 安装
```
pip install -e .
```

# 案例
## 下载模型

从 Huggingface Hub 下载一个模型，部署并渲染：
```python
from ding.bonus import TD3Agent
from huggingface_ding import pull_model_from_hub

# Pull model from Hugggingface hub
policy_state_dict, cfg=pull_model_from_hub(repo_id="OpenDILabCommunity/LunarLander-v2-TD3")
# Instantiate the agent
agent = TD3Agent(env="lunarlander_continuous",exp_name="LunarLander-v2-TD3", cfg=cfg.exp_config, policy_state_dict=policy_state_dict)
# Continue training
agent.train(step=5000)
# Render the new agent performance
agent.deploy(enable_save_replay=True)

```

## 上传模型

使用 DI-engine 训练了一个模型，并将其推送至 Huggingface Hub，制作 Modelcard
```python
from ding.bonus import TD3Agent
from huggingface_ding import push_model_to_hub

# Instantiate the agent
agent = TD3Agent("lunarlander_continuous", exp_name="LunarLander-v2-TD3")
# Train the agent
return_ = agent.train(step=int(4000000), collector_env_num=4, evaluator_env_num=4)
# Push model to huggingface hub
push_model_to_hub(
    agent=agent.best,
    env_name="OpenAI/Gym/Box2d",
    task_name="LunarLander-v2",
    algo_name="TD3",
    wandb_url=return_.wandb_url,
    github_repo_url="https://github.com/opendilab/DI-engine",
    github_doc_model_url="https://di-engine-docs.readthedocs.io/en/latest/12_policies/td3.html",
    github_doc_env_url="https://di-engine-docs.readthedocs.io/en/latest/13_envs/lunarlander.html",
    installation_guide="pip3 install DI-engine[common_env,fast]",
    usage_file_by_git_clone="./dizoo/common/td3/lunarlander_td3_deploy.py",
    usage_file_by_huggingface_ding="./dizoo/common/td3/lunarlander_td3_download.py",
    train_file="./dizoo/common/td3/lunarlander_td3.py",
    repo_id="OpenDILabCommunity/LunarLander-v2-TD3"
)
```