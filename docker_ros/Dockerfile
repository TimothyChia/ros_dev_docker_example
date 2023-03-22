# syntax=docker/dockerfile:1

FROM osrf/ros:noetic-desktop-full

# # streamline sharing files between host and container. OPTIONAL. INCOMPLETE.
# ARG USER_ID
# ARG GROUP_ID
# RUN addgroup --gid $GROUP_ID user 
# RUN adduser --disabled-password --gecos '' --uid $USER_ID --gid $GROUP_ID user
# USER user

# apparently this will stop errors such as certain apt install commands needing you to specify the nationality of your keyboard
ARG DEBIAN_FRONTEND=noninteractive

# install useful things
RUN apt-get update && apt-get install -y wget git vim unzip tmux

# install zsh
RUN apt-get update && apt-get install -y zsh && \
    chsh -s $(which zsh) && \
    sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" && \
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# tell Docker to use zsh as it builds. Probably not important.
SHELL ["usr/bin/zsh","-c"]

# configure zsh. add any desired pluigns here.
RUN echo "source /opt/ros/noetic/setup.zsh" >> ~/.zshrc && \
    sed -i 's/ZSH_THEME=\"robbyrussell\"/ZSH_THEME=\"ys\"/' ~/.zshrc && \
    sed -i 's/plugins=(git)/plugins=(git z zsh-autosuggestions extract web-search zsh-syntax-highlighting)/ '   ~/.zshrc 

# One way to persistently enable newer compiler for modern CPU compatibility.
RUN apt install -y gcc-10 g++-10
ENV CC=gcc-10
ENV CXX=g++-10

# # setup python stuff. Not needed for Ubuntu-Bionic. Note that the ros image does not come with python2.
RUN apt install -y python3-pip python-is-python3

# pytorch stuff. Note that GPU is NOT enabled in this version. see https://pytorch.org/get-started/locally/
RUN pip3 install torch==1.9.0+cpu torchvision==0.10.0+cpu torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html

RUN pip install scikit-learn
RUN pip install python-igraph
RUN pip install jupyterlab pandas

# this container is meant to have /some_folder/catkin_ws mounted in as /ext-code . This also lets you docker exec -it zsh into the workdir
RUN echo "source /ext-code/catkin_ws/devel/setup.zsh" >> ~/.zshrc
WORKDIR /ext-code

# used to solve an unknown error related to a gtsam so file not being linked.
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib

# overwrites the entrypoint the ros-images had set
ENTRYPOINT []
CMD ["/bin/zsh"]
# CMD ["zsh"]