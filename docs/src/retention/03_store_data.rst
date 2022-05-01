Store Data
==========

.. code-block:: python

    def run_command(cmd):
        print(cmd)
        os.system(cmd)

我们首先定义一个函数，可以调用os package运行shell命令，这样我们就可以通过python脚本运行shell命令，十分方便。

.. code-block:: python

    def store_data(src, device):
        cnt = 0
        files = FT.getAllFiles(src)
        pbar = tqdm(total=len(files))
        for file in files:
            cmd = f'sudo -S dd if={file} of={device} ibs=4k obs=4k skip=0 seek={cnt} < password'
            run_command(cmd)
            cnt += int(os.path.getsize(file) / 4096)
            pbar.update(1)
        pbar.close()

src为初始数据文件位置，device为设备名称，我们使用dd命令写入数据。我们的数据是一个个12G的文件，因此，我们先获取
文件列表，再依次写入文件。

.. code-block:: python

    def intel_670p():
        src = 'data/384G'
        device = '/dev/nvme1n1'
        store_data(src, device)


    def kingston_nv1():
        src = 'data/768G'
        device = '/dev/nvme2n1'
        store_data(src, device)


    def seagate_q5():
        src = 'data/384G'
        device = '/dev/nvme3n1'
        store_data(src, device)


    def samsung_870qvo():
        src = 'data/768G'
        device = '/dev/sdb'
        store_data(src, device)


    def sandisk_redgrey():
        src = 'data/96G'
        device = '/dev/sdc'
        store_data(src, device)


    def netac_u185():
        src = 'data/96G'
        device = '/dev/sdd'
        store_data(src, device)

为6种设备分别定义了对应的函数，有个小技巧是 **我这边有一个可以同时插4块NVMe SSD的设备，如果我们每次注意将对应设备插到对应的插槽，这样子
我们的设备信息，/dev/nvme1n1等信息就无需再修改，SATA SSD也类似，但是，还是需要查看确认一下，如果不小心写入到存放操作系统的SSD中，会非常惨，
数据也恢复不了。**

.. code-block:: python

    parser = argparse.ArgumentParser()
    parser.add_argument('choice', type=int)
    args = parser.parse_args()

    if args.choice == 0:
        print('choose intel')
        intel_670p()
    elif args.choice == 1:
        print('choose kingston')
        kingston_nv1()
    elif args.choice == 2:
        print('choose seagate')
        seagate_q5()
    elif args.choice == 3:
        print('choose samsung')
        samsung_870qvo()
    elif args.choice == 4:
        print('choose sandisk')
        sandisk_redgrey()
    elif args.choice == 5:
        print('choose netac')
        netac_u185()

再定义命令行输入，这样就可以方便地控制运行哪个设备的写入脚本。

.. code-block:: shell

    bash tmux.sh

.. code-block:: shell

    python 03_store_data.py 0
    python 03_store_data.py 1
    python 03_store_data.py 2
    python 03_store_data.py 3
    python 03_store_data.py 4
    python 03_store_data.py 5

最后，我们先运行tmux自动切分6个窗口的脚本，然后分别复制粘贴输入对应的python运行命令，就可以同时进行6个设备的写入了。