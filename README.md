# GCP-CROWN: General Cutting Planes for Bound-Propagation-Based Neural Network Verification

We generalize the bound propagation procedure (such as [CROWN](https://arxiv.org/pdf/1811.00866.pdf) and [β-CROWN](https://arxiv.org/pdf/2103.06624.pdf)) to allow the addition of **arbitrary cutting plane constraints**, including those involving relaxed integer variables that do not appear in existing bound propagation formulations. Our generalized bound propagation method, **GCP-CROWN**, opens up the opportunity to apply general cutting plane methods for neural network verification while benefiting from the efficiency and GPU acceleration of bound propagation methods.

As a case study, we use cutting planes generated by a MIP solver, and find that it can generate high-quality cutting planes for strengthening bound-propagation-based verifiers using GCP-CROWN. Since the branching-focused bound propagation procedure and the cutting-plane-focused MIP solver can run in parallel utilizing both GPUs and CPUs, their combination can quickly explore a large number of branches with strong cutting planes, leading to strong verification performance. GCP-CROWN is the first verifier that can **completely solve the oval20 benchmark** and verify **twice as many** instances on the oval21 benchmark compared to the best tool in VNN-COMP 2021. GCP-CROWN is part of the [α,β-CROWN](http://abcrown.org), the VNN-COMP 2022 winner.

More details can be found in our paper:

[General Cutting Planes for Bound-Propagation-Based Neural Network Verification](https://arxiv.org/pdf/2208.05740.pdf)  
**NeurIPS 2022**  
Huan Zhang\*, Shiqi Wang\*, Kaidi Xu\*, Linyi Li, Bo Li, Suman Jana, Cho-Jui Hsieh, and Zico Kolter (\*Equal contribution)

## Reproducing results

GCP-CROWN has been incorporated into our [α,β-CROWN](http://abcrown.org) verifier (alpha-beta-CROWN), which is the **winning tool of VNN-COMP 2022 and VNN-COMP 2021 with the highest total score**. To reproduce the results in the paper, please use the codebase in [α,β-CROWN repo](http://abcrown.org) following the detailed instructions below.

<p align="center">
<a href="https://abcrown.org"><img src="https://www.huan-zhang.com/images/upload/alpha-beta-crown/logo_2022.png" width="28%"></a>
</p>

### Installation and setup

Our code is tested on Python 3.7+ and PyTorch 1.11. It can be installed
easily into a conda environment. If you don't have conda, you can install
[miniconda](https://docs.conda.io/en/latest/miniconda.html).

```bash
# Clone the alpha-beta-CROWN verifier
git clone https://github.com/huanzhang12/alpha-beta-CROWN.git
cd alpha-beta-CROWN
# Remove the old environment, if necessary.
conda deactivate; conda env remove --name alpha-beta-crown
conda env create -f complete_verifier/environment.yml --name alpha-beta-crown  # install all dependents into the alpha-beta-crown environment
conda activate alpha-beta-crown  # activate the environment
```

We currently require the IBM CPLEX solver to find effective cuts for verification. It is free for students and
academics from
[here](https://community.ibm.com/community/user/datascience/blogs/xavier-nodet1/2020/07/09/cplex-free-for-students).
Please run the following commands to install CPLEX solver and compile its C++ interfacing code:

```bash
# Install IBM CPLEX >= 22.1.0
# Download from https://community.ibm.com/community/user/datascience/blogs/xavier-nodet1/2020/07/09/cplex-free-for-students
chmod +x cplex_studio2210.linux_x86_64.bin  # Any version >= 22.1.0 should work. Change executable name here.
# You can directly run the installer: ./cplex_studio2210.linux_x86_64.bin; the response.txt created below is for non-interactive installation.
cat > response.txt <<EOF
INSTALLER_UI=silent
LICENSE_ACCEPTED=true
EOF
sudo ./cplex_studio2210.linux_x86_64.bin -f response.txt
# Build the C++ code for CPLEX interface. Assumming we are still inside the alpha-beta-CROWN folder.
sudo apt install build-essential  # A modern g++ (>=8.0) is required to compile the code.
# Change CPX_PATH in complete_verifier/CPLEX_cuts/Makefile if you installed CPlex to a non-default location, like inside your home folder.
make -C complete_verifier/CPLEX_cuts/
```

### Reproducing GCP-CROWN results

All the configs to reproduce the running results are collected in `complete_verifier/exp_configs/GCP-CROWN/` folder, using the September 2022 release of the α,β-CROWN (alpha-beta-CROWN) verifier. Below we show the detailed commands for running each experiments.

To reproduce the results for oval20 models, please run
```bash
python abcrown.py --config exp_configs/GCP-CROWN/oval_base_cplex.yaml # oval20 base
python abcrown.py --config exp_configs/GCP-CROWN/oval_deep_cplex.yaml # oval20 deep
python abcrown.py --config exp_configs/GCP-CROWN/oval_wide_cplex.yaml # oval20 wide
```

To reproduce the results for VNN-COMP21 benchmarks oval21 and cifar10-resnet, please run
```bash
python abcrown.py --config exp_configs/GCP-CROWN/oval21.yaml # oval21
python abcrown.py --config exp_configs/GCP-CROWN/cifar10-resnet.yaml # cifar10-resnet
```

To reproduce the results for SDP models, please run
```bash
python abcrown.py --config exp_configs/GCP-CROWN/mnist_cnn_a_adv.yaml # mnist_cnn_a_adv
python abcrown.py --config exp_configs/GCP-CROWN/cifar_cnn_a_adv.yaml # cifar_cnn_a_adv
python abcrown.py --config exp_configs/GCP-CROWN/cifar_cnn_a_adv4.yaml # cifar_cnn_a_adv4
python abcrown.py --config exp_configs/GCP-CROWN/cifar_cnn_b_adv.yaml # cifar_cnn_b_adv
python abcrown.py --config exp_configs/GCP-CROWN/cifar_cnn_b_adv4.yaml # cifar_cnn_b_adv4
python abcrown.py --config exp_configs/GCP-CROWN/cifar_cnn_a_mix.yaml # cifar_cnn_a_mix
python abcrown.py --config exp_configs/GCP-CROWN/cifar_cnn_a_mix4.yaml # cifar_cnn_a_mix4
```


### BibTex Entry

```
@article{zhang2022gcpcrown,
  title={General Cutting Planes for Bound-Propagation-Based Neural Network Verification},
  author={Zhang, Huan and Wang, Shiqi and Xu, Kaidi and Li, Linyi and Li, Bo and Jana, Suman and Hsieh, Cho-Jui and Kolter, J Zico},
  journal={Advances in Neural Information Processing Systems},
  year={2022}
}
```
