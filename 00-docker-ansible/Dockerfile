FROM centos:7


RUN yum check-update; \
    yum install -y gcc libffi-devel python3 epel-release; \
    yum install -y python3-pip; \
    yum install -y git; \
    yum install -y wget; \
    yum install tree; \
    yum install -y ansible; \
    yum install -y jq; \
    yum clean all

RUN pip3 install --upgrade pip; \
    pip3 install --upgrade virtualenv; \
    pip3 install pywinrm[kerberos]; \
    pip3 install pywinrm; \
    pip3 install paramiko; \
    pip3 install jmspath; \
    pip3 install requests; \
    ansible-galaxy collection install cisco.ios; \
    ansible-galaxy collection install cisco.iosxr; \
    ansible-galaxy collection install juniper.device; \
    ansible-galaxy collection install f5networks.f5_modules; \
    git config --global user.name "Sebbycorp"; \
    git config --global user.email "sebastian@maniak.io";

WORKDIR /work

ENV ANSIBLE_GATHERING smart
ENV ANSIBLE_HOST_KEY_CHECKING false
ENV ANSIBLE_RETRY_FILES_ENABLED false
ENV ANSIBLE_ROLES_PATH /ansible/playbooks/roles
ENV ANSIBLE_SSH_PIPELINING True
ENV PATH /ansible/bin:$PATH
ENV PYTHONPATH /ansible/lib