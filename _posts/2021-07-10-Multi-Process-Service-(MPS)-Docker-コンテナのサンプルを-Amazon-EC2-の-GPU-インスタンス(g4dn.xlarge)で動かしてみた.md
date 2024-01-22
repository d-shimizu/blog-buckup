---
layout: post
Categories:  ["tech"]
Description:  " MPS は 1 つの GPU で複数のプロセスを並列で効率的に実行できるようにするための技術。  EC2 で利用できる GPU インスタンスの安価なものだと搭載されている GPU 数が 1 つしかないので、GPU を使うコンテナの処理を "
Tags: ["tech"]
date: "2021-07-10T19:24:00+09:00"
title: "Multi-Process Service (MPS) Docker コンテナのサンプルを Amazon EC2 の GPU インスタンス(g4dn.xlarge)で動かしてみた"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p>MPS は 1 つの <a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a> で複数のプロセスを並列で効率的に実行できるようにするための技術。</p>

<p>EC2 で利用できる <a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の安価なものだと搭載されている <a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a> 数が 1 つしかないので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a> を使うコンテナの処理を MPS を使って複数実行できないかな、と思って、公開されている MPS の Docker Compose のサンプルを試してみた。</p>

<ul>
<li><a href="https://hub.docker.com/r/nvidia/mps">mps · master · nvidia / container-images / samples · GitLab</a></li>
</ul>


<p>以下のドキュメントをみると、まだ実験的な機能であり、 Volta しかサポートしていない、というようも見え、Turing の g4dn.xlarge だと動かないかなと思ったけど、一応動いたので、やり方をメモしておく。</p>

<ul>
<li>
<a href="https://github.com/NVIDIA/nvidia-docker/wiki/MPS-(EXPERIMENTAL">MPS (EXPERIMENTAL) · NVIDIA/nvidia-docker Wiki · GitHub</a>)</li>
</ul>


<p>MPS 自体のドキュメントはこのあたり。
* <a href="https://docs.nvidia.com/deploy/mps/index.html">Multi-Process Service :: GPU Deployment and Management Documentation</a></p>
</body>

<!-- more -->

<body>
<h2>環境</h2>

<ul>
<li>Platform: <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> (g4dn.xlarge)</li>
<li>OS: <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a> 2(4.14.231-173.361.amzn2.<a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64 #1 SMP)</li>
<li>Nvida Driver Version: 460.73.01</li>
<li>Cuda: 11.2.1</li>
</ul>


<h2>手順</h2>

<p>まず、g4dn.xlarge を起動しておく。
一般的には <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> なのかもしれないが、以下は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a> 2 での手順となる。</p>

<h3>事前準備</h3>

<p>必要なパッケージをインストールする。</p>

<ul>
<li>git インストール</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ sudo yum install git </pre>


<ul>
<li>Docker Compose インストールと実行権限付与</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose </pre>


<ul>
<li>Docker Runtime の変更と反映</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ sudo sed -i"" -e 's/OPTIONS="--default-ulimit nofile=1024:4096"/OPTIONS="--default-runtime nvidia --default-ulimit nofile=1024:4096"/' /etc/sysconfig/docker </pre>




<pre class="code bash" data-lang="bash" data-unlink> $ sudo systemctl restart docker </pre>


<h3>MPS のサンプル Docker Compose の実行</h3>

<p>docker-compose には、初期化を行うコンテナ(exclusive-mode)と MPS の Service を実行するコンテナ(mps-<a class="keyword" href="http://d.hatena.ne.jp/keyword/daemon">daemon</a>)、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>を実行する3つのコンテナ(nbody)が定義されている。
mps-<a class="keyword" href="http://d.hatena.ne.jp/keyword/daemon">daemon</a> と nbody は互いにリソース共有できている必要があるが、元のままだと失敗するので、少し修正する。</p>

<ul>
<li>サンプルの取得</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ git clone https://gitlab.com/nvidia/container-images/samples.git </pre>


<ul>
<li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ移動</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ cd samples/mps </pre>


<ul>
<li>docker-compose.yml の編集 (mps-<a class="keyword" href="http://d.hatena.ne.jp/keyword/daemon">daemon</a> へ <code>ipc: shareable</code> の追記と nbody の <code>ipc: ...</code> を修正)</li>
</ul>


<pre class="code lang-yaml" data-lang="yaml" data-unlink> # Multi-Process Service (MPS) example with the nbody CUDA sample
# export NVIDIA_VISIBLE_DEVICES=0
# export CUDA_MPS_ACTIVE_THREAD_PERCENTAGE=33
# docker-compose up
version: '2.3'
services:
    # From the MPS documentation:
    # When using MPS it is recommended to use EXCLUSIVE_PROCESS mode to ensure
    # that only a single MPS server is using the GPU, which provides additional
    # insurance that the MPS server is the single point of arbitration between
    # all CUDA processes for that GPU.
    exclusive-mode:
        image: debian:stretch-slim
        command: nvidia-smi -c EXCLUSIVE_PROCESS
        # https://github.com/nvidia/nvidia-container-runtime#environment-variables-oci-spec
        # NVIDIA_VISIBLE_DEVICES will default to "all" (from file .env), unless
        # the variable is exported on the command-line.
        environment:
          - "NVIDIA_VISIBLE_DEVICES"
          - "NVIDIA_DRIVER_CAPABILITIES=utility"
        runtime: nvidia
        network_mode: none
        # CAP_SYS_ADMIN is required to modify the compute mode of the GPUs.
        # This capability is granted only to this ephemeral container, not to
        # the MPS daemon.
        cap_add:
          - SYS_ADMIN
    mps-daemon:
        image: nvidia/mps
        container_name: mps-daemon
        restart: on-failure
        # The "depends_on" only guarantees an ordering for container *start*:
        # https://docs.docker.com/compose/startup-order/
        # There is a potential race condition: the MPS or CUDA containers might
        # start before the exclusive compute mode is set. If this happens, one
        # of the CUDA application will fail to initialize since MPS will not be
        # the single point of arbitration for GPU access.
        depends_on:
          - exclusive-mode
        environment:
          - "NVIDIA_VISIBLE_DEVICES"
          - "CUDA_MPS_ACTIVE_THREAD_PERCENTAGE"
        runtime: nvidia
        init: true
        network_mode: none
        ulimits:
          memlock:
            soft: -1
            hard: -1
        ipc: shareable  # 追記
        # The MPS control daemon, the MPS server, and the associated MPS
        # clients communicate with each other via named pipes and UNIX domain
        # sockets. The default directory for these pipes and sockets is
        # /tmp/nvidia-mps.
        # Here we share a tmpfs between the applications and the MPS daemon.
        volumes:
          - nvidia_mps:/tmp/nvidia-mps
    nbody:
        # Similarly to the problem described above: a CUDA application can
        # acquire the exclusive compute context before the MPS control daemon.
        depends_on:
          - mps-daemon
        build: cuda-samples
        environment:
          - "NVIDIA_VISIBLE_DEVICES"
        runtime: nvidia
        # Share the IPC namespace with the MPS control daemon.
        ipc: service:mps-daemon
        # ipc: container:mps-daemon をコメントアウトする
        scale: 3
        volumes:
          - nvidia_mps:/tmp/nvidia-mps
volumes:
    nvidia_mps:
        driver_opts:
            type: tmpfs
            device: tmpfs
 </pre>


<ul>
<li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>セット</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ export NVIDIA_VISIBLE_DEVICES=0
$ export CUDA_MPS_ACTIVE_THREAD_PERCENTAGE=33 </pre>


<ul>
<li>起動</li>
</ul>


<pre class="code bash" data-lang="bash" data-unlink> $ /usr/local/bin/docker-compose up
Creating mps_exclusive-mode_1 ... done
Creating mps-daemon           ... done
Creating mps_nbody_1          ... done
Creating mps_nbody_2          ... done
Creating mps_nbody_3          ... done
Attaching to mps_exclusive-mode_1, mps-daemon, mps_nbody_2, mps_nbody_3, mps_nbody_1
mps-daemon        | Size of /dev/shm: 67108864 bytes
exclusive-mode_1  | Compute mode is already set to EXCLUSIVE_PROCESS for GPU 00000000:00:1E.0.
exclusive-mode_1  | All done.
mps-daemon        | CUDA_MPS_ACTIVE_THREAD_PERCENTAGE: 33
mps-daemon        | Available GPUs:
mps-daemon        |     - 0, 00000000:00:1E.0, Tesla T4, Exclusive_Process
mps-daemon        | Starting NVIDIA MPS control daemon...
mps-daemon        | [2021-06-27 07:30:24.637 Control     6] Start
mps_exclusive-mode_1 exited with code 0
mps-daemon        | [2021-06-27 07:30:26.214 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.215 Control     6] User did not send valid credentials
mps-daemon        | [2021-06-27 07:30:26.215 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.216 Control     6] NEW CLIENT 0 from user 0: Server is not ready, push client to pending list
mps-daemon        | [2021-06-27 07:30:26.216 Control     6] Starting new server 17 for user 0
mps-daemon        | [2021-06-27 07:30:26.216 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.216 Control     6] User did not send valid credentials
mps-daemon        | [2021-06-27 07:30:26.216 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.216 Control     6] NEW CLIENT 0 from user 0: Server is not ready, push client to pending list
mps-daemon        | [2021-06-27 07:30:26.218 Other    17] Start
mps-daemon        | [2021-06-27 07:30:26.218 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.807 Other    17] Volta MPS: Creating server context on device 0
mps-daemon        | [2021-06-27 07:30:26.837 Control     6] NEW SERVER 17: Ready
mps-daemon        | [2021-06-27 07:30:26.837 Other    17] activeThreadsPercentage set to 33.000000
mps-daemon        | [2021-06-27 07:30:26.837 Other    17] MPS Server is started
mps-daemon        | [2021-06-27 07:30:26.837 Other    17] Volta MPS Server: Received new client request
mps-daemon        | [2021-06-27 07:30:26.837 Other    17] MPS Server: worker created
mps-daemon        | [2021-06-27 07:30:26.837 Other    17] Volta MPS: Creating worker thread
mps-daemon        | [2021-06-27 07:30:26.838 Other    17] Volta MPS Server: Received new client request
mps-daemon        | [2021-06-27 07:30:26.838 Other    17] MPS Server: worker created
mps-daemon        | [2021-06-27 07:30:26.838 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.838 Other    17] Volta MPS: Creating worker thread
mps-daemon        | [2021-06-27 07:30:26.838 Control     6] User did not send valid credentials
mps-daemon        | [2021-06-27 07:30:26.838 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.838 Control     6] NEW CLIENT 0 from user 0: Server already exists
mps-daemon        | [2021-06-27 07:30:26.839 Other    17] Volta MPS Server: Received new client request
mps-daemon        | [2021-06-27 07:30:26.839 Other    17] Volta MPS: Creating worker thread
mps-daemon        | [2021-06-27 07:30:26.839 Other    17] MPS Server: worker created
mps-daemon        | [2021-06-27 07:30:26.851 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.852 Control     6] NEW CLIENT 0 from user 0: Server already exists
mps-daemon        | [2021-06-27 07:30:26.852 Other    17] Volta MPS Server: Received new client request
mps-daemon        | [2021-06-27 07:30:26.852 Other    17] MPS Server: worker created
mps-daemon        | [2021-06-27 07:30:26.852 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.852 Control     6] NEW CLIENT 0 from user 0: Server already exists
mps-daemon        | [2021-06-27 07:30:26.852 Other    17] Volta MPS Server: Received new client request
mps-daemon        | [2021-06-27 07:30:26.852 Other    17] Volta MPS: Creating worker thread
mps-daemon        | [2021-06-27 07:30:26.852 Other    17] Volta MPS: Device Tesla T4 (uuid 0xafc3ccca-0xc3365689-0x2ed8f0e1-0x4233036f) is associated
mps-daemon        | [2021-06-27 07:30:26.852 Other    17] MPS Server: worker created
mps-daemon        | [2021-06-27 07:30:26.853 Other    17] Volta MPS: Creating worker thread
mps-daemon        | [2021-06-27 07:30:26.853 Other    17] Volta MPS: Device Tesla T4 (uuid 0xafc3ccca-0xc3365689-0x2ed8f0e1-0x4233036f) is associated
mps-daemon        | [2021-06-27 07:30:26.853 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:30:26.853 Control     6] NEW CLIENT 0 from user 0: Server already exists
mps-daemon        | [2021-06-27 07:30:26.853 Other    17] Volta MPS Server: Received new client request
mps-daemon        | [2021-06-27 07:30:26.853 Other    17] MPS Server: worker created
mps-daemon        | [2021-06-27 07:30:26.853 Other    17] Volta MPS: Creating worker thread
mps-daemon        | [2021-06-27 07:30:26.853 Other    17] Volta MPS: Device Tesla T4 (uuid 0xafc3ccca-0xc3365689-0x2ed8f0e1-0x4233036f) is associated
nbody_1           | Run "nbody -benchmark [-numbodies=&lt;numBodies&gt;]" to measure performance.
nbody_1           |     -fullscreen       (run n-body simulation in fullscreen mode)
nbody_1           |     -fp64             (use double precision floating point values for simulation)
nbody_1           |     -hostmem          (stores simulation data in host memory)
nbody_1           |     -benchmark        (run benchmark to measure performance)
nbody_1           |     -numbodies=&lt;N&gt;    (number of bodies (&gt;= 1) to run in simulation)
nbody_1           |     -device=&lt;d&gt;       (where d=0,1,2.... for the CUDA device to use)
nbody_1           |     -numdevices=&lt;i&gt;   (where i=(number of CUDA devices &gt; 0) to use for simulation)
nbody_1           |     -compare          (compares simulation results running once on the default GPU and once on the CPU)
nbody_1           |     -cpu              (run n-body simulation on the CPU)
nbody_1           |     -tipsy=&lt;file.bin&gt; (load a tipsy model file for simulation)
nbody_1           |
nbody_1           | NOTE: The CUDA Samples are not meant for performance measurements. Results may vary when GPU Boost is enabled.
nbody_1           |
nbody_1           | &gt; Windowed mode
nbody_1           | &gt; Simulation data stored in video memory
nbody_1           | &gt; Single precision floating point simulation
nbody_1           | &gt; 1 Devices used for simulation
nbody_1           | MapSMtoCores for SM 7.5 is undefined.  Default to use 64 Cores/SM
nbody_1           | GPU Device 0: "Tesla T4" with compute capability 7.5
nbody_1           |
nbody_1           | &gt; Compute 7.5 CUDA device: [Tesla T4]
nbody_1           | 14336 bodies, total time for 10000 iterations: 31121.484 ms
nbody_1           | = 66.038 billion interactions per second
nbody_1           | = 1320.765 single-precision GFLOP/s at 20 flops per interaction
nbody_3           | Run "nbody -benchmark [-numbodies=&lt;numBodies&gt;]" to measure performance.
nbody_3           |     -fullscreen       (run n-body simulation in fullscreen mode)
nbody_3           |     -fp64             (use double precision floating point values for simulation)
nbody_3           |     -hostmem          (stores simulation data in host memory)
nbody_3           |     -benchmark        (run benchmark to measure performance)
nbody_3           |     -numbodies=&lt;N&gt;    (number of bodies (&gt;= 1) to run in simulation)
nbody_3           |     -device=&lt;d&gt;       (where d=0,1,2.... for the CUDA device to use)
nbody_3           |     -numdevices=&lt;i&gt;   (where i=(number of CUDA devices &gt; 0) to use for simulation)
nbody_3           |     -compare          (compares simulation results running once on the default GPU and once on the CPU)
nbody_3           |     -cpu              (run n-body simulation on the CPU)
nbody_3           |     -tipsy=&lt;file.bin&gt; (load a tipsy model file for simulation)
nbody_3           |
nbody_3           | NOTE: The CUDA Samples are not meant for performance measurements. Results may vary when GPU Boost is enabled.
nbody_3           |
nbody_3           | &gt; Windowed mode
nbody_3           | &gt; Simulation data stored in video memory
nbody_3           | &gt; Single precision floating point simulation
nbody_3           | &gt; 1 Devices used for simulation
nbody_3           | MapSMtoCores for SM 7.5 is undefined.  Default to use 64 Cores/SM
nbody_3           | GPU Device 0: "Tesla T4" with compute capability 7.5
nbody_3           |
nbody_3           | &gt; Compute 7.5 CUDA device: [Tesla T4]
nbody_3           | 14336 bodies, total time for 10000 iterations: 31118.779 ms
nbody_3           | = 66.044 billion interactions per second
nbody_3           | = 1320.880 single-precision GFLOP/s at 20 flops per interaction
nbody_2           | Run "nbody -benchmark [-numbodies=&lt;numBodies&gt;]" to measure performance.
nbody_2           |     -fullscreen       (run n-body simulation in fullscreen mode)
nbody_2           |     -fp64             (use double precision floating point values for simulation)
nbody_2           |     -hostmem          (stores simulation data in host memory)
nbody_2           |     -benchmark        (run benchmark to measure performance)
nbody_2           |     -numbodies=&lt;N&gt;    (number of bodies (&gt;= 1) to run in simulation)
nbody_2           |     -device=&lt;d&gt;       (where d=0,1,2.... for the CUDA device to use)
nbody_2           |     -numdevices=&lt;i&gt;   (where i=(number of CUDA devices &gt; 0) to use for simulation)
nbody_2           |     -compare          (compares simulation results running once on the default GPU and once on the CPU)
nbody_2           |     -cpu              (run n-body simulation on the CPU)
nbody_2           |     -tipsy=&lt;file.bin&gt; (load a tipsy model file for simulation)
nbody_2           |
nbody_2           | NOTE: The CUDA Samples are not meant for performance measurements. Results may vary when GPU Boost is enabled.
nbody_2           |
nbody_2           | &gt; Windowed mode
nbody_2           | &gt; Simulation data stored in video memory
nbody_2           | &gt; Single precision floating point simulation
nbody_2           | &gt; 1 Devices used for simulation
nbody_2           | MapSMtoCores for SM 7.5 is undefined.  Default to use 64 Cores/SM
nbody_2           | GPU Device 0: "Tesla T4" with compute capability 7.5
nbody_2           |
nbody_2           | &gt; Compute 7.5 CUDA device: [Tesla T4]
nbody_2           | 14336 bodies, total time for 10000 iterations: 31118.016 ms
nbody_2           | = 66.046 billion interactions per second
nbody_2           | = 1320.913 single-precision GFLOP/s at 20 flops per interaction
mps-daemon        | [2021-06-27 07:30:58.151 Other    17] Receive command failed, assuming client exit
mps-daemon        | [2021-06-27 07:30:58.151 Other    17] Volta MPS: Client disconnected. Number of active client contexts is 2
mps-daemon        | [2021-06-27 07:30:58.152 Other    17] Receive command failed, assuming client exit
mps-daemon        | [2021-06-27 07:30:58.152 Other    17] Volta MPS: Client disconnected. Number of active client contexts is 1
mps-daemon        | [2021-06-27 07:30:58.159 Other    17] Receive command failed, assuming client exit
mps-daemon        | [2021-06-27 07:30:58.159 Other    17] Volta MPS: Client disconnected. Number of active client contexts is 0
mps-daemon        | [2021-06-27 07:30:58.167 Other    17] Receive command failed, assuming client exit
mps-daemon        | [2021-06-27 07:30:58.167 Other    17] Volta MPS: Client process disconnected
mps-daemon        | [2021-06-27 07:30:58.186 Other    17] Receive command failed, assuming client exit
mps-daemon        | [2021-06-27 07:30:58.186 Other    17] Volta MPS: Client process disconnected
mps-daemon        | [2021-06-27 07:30:58.201 Other    17] Receive command failed, assuming client exit
mps-daemon        | [2021-06-27 07:30:58.202 Other    17] Volta MPS: Client process disconnected
mps_nbody_1 exited with code 0
mps_nbody_3 exited with code 0
mps_nbody_2 exited with code 0
mps-daemon        | [2021-06-27 07:31:23.573 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:31:23.573 Control     6] NEW UI
mps-daemon        | [2021-06-27 07:31:23.573 Control     6] Cmd:get_server_list
mps-daemon        | [2021-06-27 07:31:23.573 Control     6] 17
mps-daemon        | [2021-06-27 07:31:23.573 Control     6] UI closed
mps-daemon        | [2021-06-27 07:32:23.815 Control     6] Accepting connection...
mps-daemon        | [2021-06-27 07:32:23.815 Control     6] NEW UI
mps-daemon        | [2021-06-27 07:32:23.815 Control     6] Cmd:get_server_list
mps-daemon        | [2021-06-27 07:32:23.815 Control     6] 17
mps-daemon        | [2021-06-27 07:32:23.815 Control     6] UI closed </pre>


<p>nbody_1, nbody_2, nbody_3 で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>が実行され、結果が出力されていて、動作していることがわかった。</p>

<h2>まとめ</h2>

<p>MPS Docker コンテナのサンプルを <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> の <a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>(g4dn.xlarge)で動かしてみた。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>プロセス(nbody_1,2,3) 実行中の <a class="keyword" href="http://d.hatena.ne.jp/keyword/nvidia">nvidia</a>-smi の結果を記載していないが、nbody_1,2,3 と MPS の Docker のプロセスが実行されていたので、動作はしていると思う。
パフォーマンスがどうなのかは比較していないので不明。</p>

<h2>参考</h2>

<ul>
<li><a href="https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf">CUDA Multi-Process Service (MPS) - NVIDIA Developer</a></li>
<li><a href="https://docs.nvidia.com/deploy/mps/index.html">Multi-Process Service :: GPU Deployment and Management Documentation</a></li>
<li><a href="https://hub.docker.com/r/nvidia/mps">mps · master · nvidia / container-images / samples · GitLab</a></li>
<li><a href="https://medium.com/nttlabs/nvidia-mps-vgpu-mig-f975ca39122e">NVIDIAのGPUリソース分割技術 - nttlabs - Medium</a></li>
<li><a href="https://zenn.dev/sotetsuk/articles/41d5b0e1b7f71020a14b">MPSを使ってシングルGPUで複数プロセスを効率的に並列実行する</a></li>
<li><a href="https://github.com/docker/compose/issues/8127">IPC - Cannot create IPC shared memory containers · Issue #8127 · docker/compose</a></li>
</ul>

</body>
