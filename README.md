# OpenShift Tomcat Community Cartridge

## Use it

In OpenShift, choose a downloaded cartridge, with the following URL : http://cartreflect-claytondev.rhcloud.com/github/worldline/openshift-cartridge-tomcat

## How this cartridge was created

This cartidge was forked from https://github.com/openshift/origin-server/tree/master/cartridges/openshift-origin-cartridge-jbossews

Every JbossEWS reference was removed and replaced by tomcat.

## Step used to create this cartridge

```
git clone https://github.com/openshift/origin-server.git
cd origin-server
git filter-branch --subdirectory-filter cartridges/openshift-origin-cartridge-jbossews
rename jbossews tomcat *
rename JBOSSEWS TOMCAT env/*
find . -not -path '*/\.git/*' -type f -exec sed -i "s/JBOSSEWS/TOMCAT/g" {} \;
find . -not -path '*/\.git/*' -type f -exec sed -i "s/JBOSS/TOMCAT/g" {} \;
find . -not -path '*/\.git/*' -type f -exec sed -i "s/jboss-ews-2/tomcat-7/g" {} \;
find . -not -path '*/\.git/*' -type f -exec sed -i "s/jboss-ews-1/tomcat-6/g" {} \;
sed -i "s/jboss.com/tomcat/g" metadata/manifest.yml
sed -i "s/Website: .*/Website: http:\/\/tomcat.apache.org\//g" metadata/manifest.yml
sed -i "s/ (JBoss EWS .*)//g" metadata/manifest.yml
sed -i "s/1.0/6.0/g" metadata/manifest.yml
sed -i "s/2.0/7.0/g" metadata/manifest.yml
sed -i "s/redhat/worldline/g" metadata/manifest.yml
sed -i "s/JBossEWS2.0/Tomcat7.0/g" openshift-origin-cartridge-tomcat.spec
find . -not -path '*/\.git/*' -type f -exec sed -i "s/jboss/tomcat/g" {} \;
find . -not -path '*/\.git/*' -type f -exec sed -i "s/JBossEWS/Tomcat/g" {} \;
find . -not -path '*/\.git/*' -type f -exec sed -i "s/JBoss/Tomcat/g" {} \;
mv versions/1.0 versions/6.0
mv versions/2.0 versions/7.0
```

Then I add this block in `bin/setup`, so that it works on OpenShift Online

```
SYSTEM_TOMCAT_DIR="/etc/alternatives/apache-tomcat-${version}"

# if SYSTEM_TOMCAT_DIR doesn't exists change it, to use a local tomcat
if [ ! -d "$SYSTEM_TOMCAT_DIR" ]; then
  SYSTEM_TOMCAT_DIR="${OPENSHIFT_DATA_DIR}/apache-tomcat-${version}"
  rm -fr "${SYSTEM_TOMCAT_DIR}*"
  pushd "${OPENSHIFT_DATA_DIR}"
  if [ "${version}" == "6.0" ]; then
    VERSION="6.0.39"
    wget "http://psg.mtu.edu/pub/apache/tomcat/tomcat-6/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz"
    tar xvzf ${OPENSHIFT_DATA_DIR}/apache-tomcat-${VERSION}.tar.gz
    rm -f ${OPENSHIFT_DATA_DIR}/apache-tomcat-${VERSION}.tar.gz
    ln -s ${OPENSHIFT_DATA_DIR}/apache-tomcat-${VERSION} ${OPENSHIFT_DATA_DIR}/apache-tomcat-6.0
  else
    VERSION="7.0.52"
    wget "http://psg.mtu.edu/pub/apache/tomcat/tomcat-7/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz"
    tar xvzf ${OPENSHIFT_DATA_DIR}/apache-tomcat-${VERSION}.tar.gz
    rm -f ${OPENSHIFT_DATA_DIR}/apache-tomcat-${VERSION}.tar.gz
    ln -s ${OPENSHIFT_DATA_DIR}/apache-tomcat-${VERSION} ${OPENSHIFT_DATA_DIR}/apache-tomcat-7.0
  fi
  popd
fi
```

## Install system dependecies

    $ yum install bc java-1.6.0-openjdk-devel java-1.7.0-openjdk-devel

## Download Tomcat 6 and Tomcat 7 and install them in `/opt/apache-tomcat-X.Y`

    $ cd /opt
    $ wget http://download.nextag.com/apache/tomcat/tomcat-7/v7.0.41/bin/apache-tomcat-7.0.41.tar.gz
    $ tar xvzf apache-tomcat-7.0.41.tar.gz
    $ ln -s /opt/apache-tomcat-7.0.41 /opt/apache-tomcat-7.0
    $ wget http://psg.mtu.edu/pub/apache/tomcat/tomcat-6/v6.0.37/bin/apache-tomcat-6.0.37.tar.gz
    $ tar xvzf apache-tomcat-6.0.37.tar.gz
    $ ln -s /opt/apache-tomcat-6.0.37 /opt/apache-tomcat-6.0

## Test as a download cartridge

Make sure this option is enable the the broker config.

    DOWNLOAD_CARTRIDGES_ENABLED="true"

Then create a cartridge with this URL: <http://cartreflect-claytondev.rhcloud.com/github/worldline/openshift-cartridge-tomcat>

## Install as a RPM

Build the RPM.

    $ yum install tito
    $ tito init # only the first time you use tito for this cartridge
    $ tito tag
    $ tito build --rpm --test
    ...
    Successfully built: /tmp/tito/openshift-origin-cartridge-tomcat-0.6.2-1.git.0.bed44cb.el6.src.rpm /tmp/tito/noarch/openshift-origin-cartridge-tomcat-0.6.2-1.git.0.bed44cb.el6.noarch.rpm

On the node

    $ yum install /tmp/tito/noarch/openshift-origin-cartridge-tomcat-0.6.2-1.git.0.bed44cb.el6.noarch.rpm

On the broker

    $ oo-admin-broker-cache --clear --console



----- Original README ------

# OpenShift Tomcat Cartridge
This cartridge is documented in the [Cartridge Guide](http://openshift.github.io/documentation/oo_cartridge_guide.html#tomcat).
