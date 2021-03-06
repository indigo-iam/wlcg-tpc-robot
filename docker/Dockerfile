FROM centos:7

ARG TEST_USER=test
ARG TEST_USER_UID=501

ENV TEST_USER $TEST_USER
ENV TEST_USER_UID $TEST_USER_UID

RUN   yum clean all && \
      yum install -y hostname epel-release && \
      yum -y update && \
      yum -y install which wget tar gcc openssl-devel sudo file make which && \
      echo '%wheel ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
      adduser --uid ${TEST_USER_UID} ${TEST_USER} && \
      usermod -a -G wheel ${TEST_USER} && \
      mkdir /home/test/.config && chown test:test /home/test/.config && \
      yum -y install telnet nc

RUN cd /root && \
    wget https://curl.haxx.se/download/curl-7.72.0.tar.gz && \
    tar xvzf curl-7.72.0.tar.gz && \
    cd curl-7.72.0 && \
    ./configure --with-ssl --disable-dependency-tracking && \
    make && make install && \
    cd .. && rm -rf curl-7.72.0 && rm curl-7.72.0.tar.gz

RUN wget https://ci.cloud.cnaf.infn.it/view/repos/job/repo_test_ca/lastSuccessfulBuild/artifact/test-ca.repo -O /etc/yum.repos.d/test-ca.repo && \
    yum -y clean all && yum -y install igi-test-ca && \
    mkdir -p /home/test/.globus && cp /usr/share/igi-test-ca/test0.p12 /home/test/.globus/usercred.p12 && \
    chmod 600 /home/test/.globus/usercred.p12 && \
    chown -R test:test /home/test/.globus

RUN yum -y install voms-clients-java gfal2-util gfal2-all gfal2-python davix jq && \
    yum clean all && \
    yum -y install https://github.com/indigo-dc/oidc-agent/releases/download/v3.3.5/oidc-agent-3.3.5-1.el7.x86_64.rpm && \
    yum install -y python-pip python-enum && \
    pip install --upgrade pip && \
    pip install --upgrade robotframework-httplibrary && \
    pip install --upgrade oic && \
    pip install --upgrade requests && \
    pip install --upgrade pyyaml && \
    pip install --upgrade shyaml && \
    pip install --upgrade oidc && \
    rm -rf /var/cache/yum

ENV TINI_VERSION v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]

USER test
WORKDIR /home/test

