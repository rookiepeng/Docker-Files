ARG BASE_CONTAINER=jupyter/base-notebook
FROM $BASE_CONTAINER

LABEL maintainer="rookiepeng <zpeng.me@gmail.com>"

USER $NB_UID

COPY lab /home/$NB_USER/.jupyter/lab

RUN conda install --quiet --yes \
    'numpy' \
    'matplotlib' \
    'pandas' \
    'scipy' \
    'seaborn' \
    'xlrd' \
    'plotly' \
    'psutil' \
    'autopep8' \
    'pandoc' \
    'ipywidgets' \
    'h5py' \
    'conda-forge::jupyter_contrib_nbextensions' \
    && \
    conda clean --all -f -y && \
    # Activate ipywidgets extension in the environment that runs the notebook server
    jupyter nbextension enable --py widgetsnbextension --sys-prefix && \
    jupyter nbextension enable code_prettify/autopep8 && \
    jupyter nbextension enable collapsible_headings/main && \
    jupyter nbextension enable hide_header/main && \
    # Also activate ipywidgets extension for JupyterLab
    # Check this URL for most recent compatibilities
    # https://github.com/jupyter-widgets/ipywidgets/tree/master/packages/jupyterlab-manager
    # jupyter labextension install @jupyter-widgets/jupyterlab-manager@^1.0.1 --no-build && \
    # jupyter labextension install jupyterlab_bokeh@1.0.0 --no-build && \
    jupyter labextension install jupyterlab-plotly && \
    jupyter labextension install @ryantam626/jupyterlab_code_formatter && \
    pip install jupyterlab_code_formatter && \
    jupyter serverextension enable --py jupyterlab_code_formatter && \
    jupyter lab build --dev-build=False && \
    npm cache clean --force && \
    rm -rf $CONDA_DIR/share/jupyter/lab/staging && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    rm -rf /home/$NB_USER/.node-gyp && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

# for plotly-orca
RUN conda install --quiet --yes -c plotly python-kaleido
RUN conda clean --all -f -y

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME /home/$NB_USER/.cache/
ENV JUPYTER_ENABLE_LAB 1

USER root
RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
    fix-permissions /home/$NB_USER

# Switch back to notebook to avoid accidental container runs as root
USER $NB_UID
