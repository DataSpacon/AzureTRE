FROM python:3.8-slim-buster

# Specify the Porter version to install
ENV PORTER_PERMALINK=v0.38.7

# Install Azure CLI
RUN apt-get update \
    && apt-get -y install ca-certificates curl apt-transport-https lsb-release gnupg \
    && curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /etc/apt/trusted.gpg.d/microsoft.gpg > /dev/null \
    && AZ_REPO=$(lsb_release -cs) \
    && echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | tee /etc/apt/sources.list.d/azure-cli.list \
    && apt-get update && apt-get -y install azure-cli

# Install Terraform
RUN apt-get update && apt-get install -y gnupg software-properties-common curl \
    && curl -fsSL https://apt.releases.hashicorp.com/gpg |  apt-key add - \
    && apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
    && apt-get update && apt-get install -y terraform

# Install Porter
RUN export PORTER_HOME=/root/.porter && curl -L https://cdn.porter.sh/${PORTER_PERMALINK}/install-linux.sh | bash

# Install Porter Azure plugin, see https://porter.sh/plugins/azure/
RUN /root/.porter/porter plugin install azure --url https://cdn.porter.sh/plugins/azure

# Install Docker
RUN apt-get update &&  apt-get install -y apt-transport-https ca-certificates curl gnupg  lsb-release \
    && curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" \
    | tee /etc/apt/sources.list.d/docker.list > /dev/null \
    && apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io

# Install jq
RUN apt-get install -y jq

ENV PATH /root/.porter/:$PATH

ENV PYTHONPATH .

COPY ./vmss_porter/requirements.txt .
RUN pip3 install -r requirements.txt

COPY . /app

WORKDIR /app/

RUN chmod +x ./run.sh

CMD ["./run.sh"]