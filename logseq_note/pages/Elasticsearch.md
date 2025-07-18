- Linux二进制安装
	- 下载安装包：elasticsearch-7.17.22-linux-x86_64.tar.gz
	- ```
	  #!/bin/bash
	  
	  CUR_DIR=$(dirname "$(readlink -f "$0")")
	  echo -e "ES dir: $CUR_DIR"
	  
	  cd $CUR_DIR
	  
	  tar -zxf elasticsearch-7.17.22-linux-x86_64.tar.gz
	  mv elasticsearch-7.17.22 es
	  mv es/config/elasticsearch.yml es/config/elasticsearch.yml.bak
	  cp elasticsearch.yml es/config/
	  
	  mv es/bin/elasticsearch-env es/bin/elasticsearch-env.bak
	  cp elasticsearch-env es/bin/
	  
	  useradd esadmin
	  chown -R esadmin:esadmin $CUR_DIR/es
	  
	  # cd es
	  # su esadmin -c 'bin/elasticsearch -d'  # 非root用户启动es
	  ```
	- elasticsearch.yml
		- ```
		  cluster.name: "docker-cluster"
		  network.host: 0.0.0.0
		  
		  # 如果是docker需要配置本机真实IP
		  # network.publish_host: YOUR_HOST_IP
		  
		  # 不显示告警提示
		  ingest.geoip.downloader.enabled: false
		  
		  # 开启跨域访问支持，默认为false
		  # 跨域访问允许的域名地址，(允许所有域名)以上使用正则
		  # http.cors.enabled: true
		  # http.cors.allow-origin: /.*/
		  
		  # 选择启动方式: 二选一
		  
		  # 设置ES为单机启动
		  # discovery.type: single-node
		  
		  # 设置ES为集群启动(单机版)
		  node.name: "node-1"
		  discovery.seed_hosts: ["127.0.0.1", "[::1]"]
		  cluster.initial_master_nodes: ["node-1"]
		  ```
	- elasticsearch-env
		- ```
		  #!/bin/bash
		  
		  set -e -o pipefail
		  
		  CDPATH=""
		  
		  SCRIPT="$0"
		  
		  # SCRIPT might be an arbitrarily deep series of symbolic links; loop until we
		  # have the concrete path
		  while [ -h "$SCRIPT" ] ; do
		    ls=`ls -ld "$SCRIPT"`
		    # Drop everything prior to ->
		    link=`expr "$ls" : '.*-> \(.*\)$'`
		    if expr "$link" : '/.*' > /dev/null; then
		      SCRIPT="$link"
		    else
		      SCRIPT=`dirname "$SCRIPT"`/"$link"
		    fi
		  done
		  
		  # determine Elasticsearch home; to do this, we strip from the path until we find
		  # bin, and then strip bin (there is an assumption here that there is no nested
		  # directory under bin also named bin)
		  ES_HOME=`dirname "$SCRIPT"`
		  
		  # now make ES_HOME absolute
		  ES_HOME=`cd "$ES_HOME"; pwd`
		  
		  while [ "`basename "$ES_HOME"`" != "bin" ]; do
		    ES_HOME=`dirname "$ES_HOME"`
		  done
		  ES_HOME=`dirname "$ES_HOME"`
		  
		  # now set the classpath
		  ES_CLASSPATH="$ES_HOME/lib/*"
		  
		  # # now set the path to java
		  # if [ ! -z "$ES_JAVA_HOME" ]; then
		  #   JAVA="$ES_JAVA_HOME/bin/java"
		  #   JAVA_TYPE="ES_JAVA_HOME"
		  # elif [ ! -z "$JAVA_HOME" ]; then
		  #   # fallback to JAVA_HOME
		  #   echo "warning: usage of JAVA_HOME is deprecated, use ES_JAVA_HOME" >&2
		  #   JAVA="$JAVA_HOME/bin/java"
		  #   JAVA_TYPE="JAVA_HOME"
		  # else
		  #   # use the bundled JDK (default)
		  #   if [ "$(uname -s)" = "Darwin" ]; then
		  #     # macOS has a different structure
		  #     JAVA="$ES_HOME/jdk.app/Contents/Home/bin/java"
		  #   else
		  #     JAVA="$ES_HOME/jdk/bin/java"
		  #   fi
		  #   JAVA_TYPE="bundled JDK"
		  # fi
		  
		  # use the bundled JDK (default)
		  # 让ES使用自带的JDK
		  if [ "$(uname -s)" = "Darwin" ]; then
		    # macOS has a different structure
		    JAVA="$ES_HOME/jdk.app/Contents/Home/bin/java"
		  else
		    JAVA="$ES_HOME/jdk/bin/java"
		  fi
		  JAVA_TYPE="bundled JDK"
		  
		  
		  
		  if [ ! -x "$JAVA" ]; then
		    echo "could not find java in $JAVA_TYPE at $JAVA" >&2
		    exit 1
		  fi
		  
		  # do not let JAVA_TOOL_OPTIONS slip in (as the JVM does by default)
		  if [ ! -z "$JAVA_TOOL_OPTIONS" ]; then
		    echo "warning: ignoring JAVA_TOOL_OPTIONS=$JAVA_TOOL_OPTIONS"
		    unset JAVA_TOOL_OPTIONS
		  fi
		  
		  # JAVA_OPTS is not a built-in JVM mechanism but some people think it is so we
		  # warn them that we are not observing the value of $JAVA_OPTS
		  if [ ! -z "$JAVA_OPTS" ]; then
		    echo -n "warning: ignoring JAVA_OPTS=$JAVA_OPTS; "
		    echo "pass JVM parameters via ES_JAVA_OPTS"
		  fi
		  
		  if [[ "$("$JAVA" -version 2>/dev/null)" =~ "Unable to map CDS archive" ]]; then
		    XSHARE="-Xshare:off"
		  else
		    XSHARE="-Xshare:auto"
		  fi
		  
		  # check the Java version
		  "$JAVA" "$XSHARE" -cp "$ES_CLASSPATH" org.elasticsearch.tools.java_version_checker.JavaVersionChecker
		  
		  export HOSTNAME=$HOSTNAME
		  
		  if [ -z "$ES_PATH_CONF" ]; then ES_PATH_CONF="$ES_HOME"/config; fi
		  
		  if [ -z "$ES_PATH_CONF" ]; then
		    echo "ES_PATH_CONF must be set to the configuration path"
		    exit 1
		  fi
		  
		  # now make ES_PATH_CONF absolute
		  ES_PATH_CONF=`cd "$ES_PATH_CONF"; pwd`
		  
		  ES_DISTRIBUTION_FLAVOR=default
		  ES_DISTRIBUTION_TYPE=tar
		  ES_BUNDLED_JDK=true
		  
		  if [[ "$ES_BUNDLED_JDK" == "false" ]]; then
		    echo "warning: no-jdk distributions that do not bundle a JDK are deprecated and will be removed in a future release" >&2
		  fi
		  
		  if [[ "$ES_DISTRIBUTION_TYPE" == "docker" ]]; then
		    # Allow environment variables to be set by creating a file with the
		    # contents, and setting an environment variable with the suffix _FILE to
		    # point to it. This can be used to provide secrets to a container, without
		    # the values being specified explicitly when running the container.
		    source "$ES_HOME/bin/elasticsearch-env-from-file"
		  
		    # Parse Docker env vars to customize Elasticsearch
		    #
		    # e.g. Setting the env var cluster.name=testcluster or ES_CLUSTER_NAME=testcluster
		    #
		    # will cause Elasticsearch to be invoked with -Ecluster.name=testcluster
		    #
		    # see https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html#_setting_default_settings
		  
		    declare -a es_arg_array
		  
		    # Elasticsearch settings need to either:
		    # a. have at least two dot separated lower case words, e.g. `cluster.name`, or
		    while IFS='=' read -r envvar_key envvar_value; do
		      if [[ -n "$envvar_value" ]]; then
		        es_opt="-E${envvar_key}=${envvar_value}"
		        es_arg_array+=("${es_opt}")
		      fi
		    done <<< "$(env | grep -E '^[-a-z0-9_]+(\.[-a-z0-9_]+)+=')"
		  
		    # b. be upper cased with underscore separators and prefixed with `ES_SETTING_`, e.g. `ES_SETTING_CLUSTER_NAME`.
		    #    Underscores in setting names are escaped by writing them as a double-underscore e.g. "__"
		    while IFS='=' read -r envvar_key envvar_value; do
		      if [[ -n "$envvar_value" ]]; then
		        # The long-hand sed `y` command works in any sed variant.
		        envvar_key="$(echo "$envvar_key" | sed -e 's/^ES_SETTING_//; s/_/./g ; s/\.\./_/g; y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/' )"
		        es_opt="-E${envvar_key}=${envvar_value}"
		        es_arg_array+=("${es_opt}")
		      fi
		    done <<< "$(env | grep -E '^ES_SETTING(_{1,2}[A-Z]+)+=')"
		  
		    # Reset the positional parameters to the es_arg_array values and any existing positional params
		    set -- "$@" "${es_arg_array[@]}"
		  
		    # The virtual file /proc/self/cgroup should list the current cgroup
		    # membership. For each hierarchy, you can follow the cgroup path from
		    # this file to the cgroup filesystem (usually /sys/fs/cgroup/) and
		    # introspect the statistics for the cgroup for the given
		    # hierarchy. Alas, Docker breaks this by mounting the container
		    # statistics at the root while leaving the cgroup paths as the actual
		    # paths. Therefore, Elasticsearch provides a mechanism to override
		    # reading the cgroup path from /proc/self/cgroup and instead uses the
		    # cgroup path defined the JVM system property
		    # es.cgroups.hierarchy.override. Therefore, we set this value here so
		    # that cgroup statistics are available for the container this process
		    # will run in.
		    export ES_JAVA_OPTS="-Des.cgroups.hierarchy.override=/ $ES_JAVA_OPTS"
		  fi
		  
		  cd "$ES_HOME"
		  
		  ```
	- 配置服务自启
		- ```
		  [Unit]
		  Description=The redis-server Process Manager
		  After=syslog.target network.target
		  
		  [Service]
		  
		  LimitCORE=unlimited
		  LimitNOFILE=65535
		  LimitNPROC=65535
		  LimitMEMLOCK=infinity
		  
		  User=esadmin
		  Group=esadmin
		  #Type=forking
		  ExecStart=YOUR_ES_PATH/bin/elasticsearch
		  Restart=always
		  [Install]
		  WantedBy=multi-user.target
		  
		  ```