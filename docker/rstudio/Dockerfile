FROM --platform=amd64 rocker/geospatial:latest

# install additional system libraries
## openssh-client is needed for GitHub SSH authentication
## libxt-dev is needed for rgl package (XQuartz/X11)
## python for DVC use
RUN apt update && apt install -y \
    openssh-client libxt-dev \
    # Python
    python3 python3-pip

# RInstall additional R packages 
## package manager, plot viewer, syntax highlghtning, pipline tool
RUN R -e "install.packages(c('renv', 'httpgd' 'languageserversetup', 'targets'))"
RUN R -e "languageserversetup::languageserver_install()"
RUN R -e "languageserversetup::languageserver_add_to_rprofile()"

# DVC Path
ENV PATH $PATH:~/.pip/bin

# Package Cahce & Permission
RUN cd /home/rstudio && mkdir .pip .cache .cache/R .cache/R/renv && \
    chown rstudio:rstudio .pip .cache .cache/R .cache/R/renv