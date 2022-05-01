Load Data
=========

.. code-block:: python

    def load_data(src, dst, device):
        cnt = 0
        FT.mkPath(dst)
        files = FT.getAllFiles(src)
        pbar = tqdm(total=len(files))
        for file in files:
            dst_file = Path(dst) / Path(file).name
            cmd = f'touch {dst_file}'
            run_command(cmd)
            temp_cnt = int(os.path.getsize(file) / 4096)
            cmd = f'sudo -S dd if={device} of={dst_file} ibs=4k obs=4k skip={cnt} seek={0} count={temp_cnt} < password'
            run_command(cmd)
            cnt += temp_cnt
            pbar.update(1)
        pbar.close()


src为初始数据文件位置，dst为读取出来的数据的存放位置，device为设备名称，我们使用dd命令读取数据。我们之前写入的是一个个12G的文件，我们想要将对应
的数据以相同的形式读取出来，因此，我们还指定了src信息，这样子我们就知道我们写入的文件的名称，可以把读取的数据以同样的形式组织起来，方便后续的比较。

.. code-block:: python

    pe_cycle = 50
    retention = 30

我们首先需要先指定一些全局信息，如上述代码中进行数据读取的6个设备为50P/E Cycle，数据存放的时间为30天。

.. code-block:: python

    def intel_670p():
        type = 'SSD_Intel_670P'
        capacity = 768
        device = '/dev/nvme1n1'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        load_data(src, dst, device)


    def kingston_nv1():
        type = 'SSD_Kingston_NV1'
        capacity = 768
        device = '/dev/nvme3n1'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        load_data(src, dst, device)


    def seagate_q5():
        type = 'SSD_Seagate_Q5'
        capacity = 384
        device = '/dev/nvme2n1'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        load_data(src, dst, device)


    def samsung_870qvo():
        type = 'SSD_SAMSUNG_870QVO'
        capacity = 768
        device = '/dev/sdb'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        load_data(src, dst, device)


    def sandisk_redgrey():
        type = 'TF_SanDisk_RedGrey'
        capacity = 96
        device = '/dev/sdd'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        load_data(src, dst, device)


    def netac_u185():
        type = 'USB_Netac_U185'
        capacity = 96
        device = '/dev/sdc'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        load_data(src, dst, device)

同样地，我们为6种设备分别定义了对应的函数， **还是一样地，对应设备的设备信息要记得修改**。我们根据type/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g的形式
进行存放，这样子可以很方便地区分。

.. code-block:: shell

    bash tmux.sh

.. code-block:: shell

    python 04_load_data.py 0
    python 04_load_data.py 1
    python 04_load_data.py 2
    python 04_load_data.py 3
    python 04_load_data.py 4
    python 04_load_data.py 5

最后，我们先运行tmux自动切分6个窗口的脚本，然后分别复制粘贴输入对应的python运行命令，就可以同时进行6个设备的读取了。