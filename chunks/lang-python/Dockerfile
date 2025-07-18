ARG base
FROM ${base}

ARG PYTHON_VERSION

USER gitpod
# Dazzle does not rebuild a layer until one of its lines are changed. Increase this counter to rebuild this layer.
ENV TRIGGER_REBUILD=2

ENV PATH="$HOME/.pyenv/bin:$HOME/.pyenv/shims:$PATH"
ENV PIPENV_VENV_IN_PROJECT=true
ENV PYENV_ROOT="$HOME/.pyenv"

# configure the default intepreter for PyCharm
ENV PYCHARM_PYTHON_PATH="$HOME/.pyenv/shims/python"

RUN sudo install-packages \
	# Install python compiling dependencies for pyenv
	python3-pip make build-essential libssl-dev zlib1g-dev \
	libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
	libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev \
	# Install PYENV
	&& git clone https://github.com/pyenv/pyenv.git ~/.pyenv \
	# v2.6.2
	&& git -C ~/.pyenv checkout a71a378dacd210f5673550ff923715bf4d04324f \
	&& pyenv install ${PYTHON_VERSION} \
	&& pyenv global ${PYTHON_VERSION} \
	&& for exec in global; do printf '%s\n' 'source "$HOME/.gp_pyenv.d/userbase.bash"' >> "$PYENV_ROOT/libexec/pyenv-$exec"; done \
	# Install additional python packages
	&& python3 -m pip install --no-cache-dir --upgrade pip \
	&& pip install --no-cache-dir --upgrade \
	setuptools wheel virtualenv pipenv pylint rope flake8 \
	mypy autopep8 pep8 pylama pydocstyle bandit notebook \
	twine \
	# Install poetry
	&& curl -sSL https://install.python-poetry.org | python \
 	&& curl -LsSf https://astral.sh/uv/install.sh | sh \
	&& sudo rm -rf /tmp/*

# update `setuptools` used by our virtual environment
RUN bash -c "source /home/gitpod/.local/share/pypoetry/venv/bin/activate \
	&& pip install --upgrade setuptools \
	&& deactivate"

COPY --chown=gitpod:gitpod pyenv.d $HOME/.gp_pyenv.d
COPY --chown=gitpod:gitpod userbase.bash $HOME/.gp_pyenv.d
COPY --chown=gitpod:gitpod python_hook.bash $HOME/.bashrc.d/60-python
COPY --chown=gitpod:gitpod avoid_userbase_hook.bash $HOME/.pyenv/pyenv.d/exec
