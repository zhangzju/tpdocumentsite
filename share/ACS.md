
# 一、信道基本常识

## 1.1 2.4G频段
    
频段范围：2412～2484MHz,共有1～14信道。各个信道在不同国家的支持其情况如下：

| Country | Channel 1~11 | Channel 12~13 |Channel 14 |
| :------:| :----------: | :-----------: | :-------: |
| North America| Yes | No  | No  |
| Japan        | Yes | Yes | Yes |
| other countries | Yes | Yes | No |
*Note: 日本支持14信道，但是仅支持11b模式, 详情请查阅相关标准*

![Alt text](imgs/acs_channel/2.4G_channel_info.png)

## 1.2 5G频段

信道数：100多个 

4个Bands，Band1 ~ Band4

|  | Band1  | Band2 |Band3 |Band4 |
| :------:| :----------: | :-----------: | :-------: |:-------: |
| channel  | 36~48   | 50~64   | 100-144 |149-165  |
| Frequency|5180~5240|5250~5320|5500~5720|5745~5825|
|CE|Support|No|No|Support|
|FCC|Support|Depends|Depends|Most Support|
|China|Support|No|No|Support|
**Band2 and Band3 是 DFS band, 雷达工作在这两个band，是否可以使用这两个band取决于各自国家的规定**

## 1.3 信道带宽

信道带宽类型： 20MHz，40MHz，80MHz（Only for 5G），160MHz（Only for 5G）

带宽40MHz信道： 2.4G: 1L~9L,5U~13U; 5G: band1（36L,40U,44L,48U）其他Band可参考下面的5G带宽分布图。

带宽80MHz信道： 仅存在于5G，每一个band都支持，eg:36/80

带宽160MHz信道: 仅存在于5G，2个band加一起，或者band 3

## 1.4 中心频率、上偏、下偏
主信道/控制信道：AP宣告其所在的信道

上偏:扩展信道频率高于控制信道频率

下偏:扩展信道频率低于控制信道频率

扩展信道：当信道带宽不小于40MHz时，存在扩展信道，ext20，ext40。扩展信道和其上下偏有关。

信道中心频率：（主信道频率 + 扩展信道频率）/ 2
    
    2.4G 5U 代表控制信道在5信道，扩展信道在1信道，中心频率在3信道
    2.4G 1L 代表控制信道在1信道，扩展信道在5信道，中心频率在3信道

![Alt text](imgs/acs_channel/40MHz_频宽.jpg)
![Alt text](imgs/acs_channel/5G信道图.png)

参考： <https://en.wikipedia.org/wiki/List_of_WLAN_channels>


# 二、 BRCM自动信道选择(ACS)

## 2.1 目标和模式

目标：带宽优先，然后再考虑信道，选择合适的信道和带宽，使无线性能最优

模式：

（1）Mode 1（ channel auto， bandwidth auto）

（2）Mode 2（ channel auto， bandwidth select）

（3）Mode 3（ channel select， bandwidth auto）

## 2.2 ACS 策略

**CCA相关参数**
![Alt text](imgs/acs_channel/CCA_params.png)

信道选择依据：根据选定策略，计算各个信道得分，选择得分最高的信道.
![Alt text](imgs/acs_channel/score_caculate.jpg)
如下图中是BCM提供所有信道选择的策略.各个因子（BSS，BUSY）的得分通过统计CCA的相关参数得出。
![Alt text](imgs/acs_channel/acs_policy_all.png)

| factor | Explain | 
| :------:| :---------- | 
| bgn| background noise threshold, 表示当其大于-65dBm时,该信道无效，配置**ACS_FLAGS_INTF_THRES_BGN**生效 |
| itf        | Interference threshold，参考 CCA类型 CCASTATS_NOPKT统计,表示当其大于40时,该信道无效.配置**ACS_FLAGS_INTF_THRES_BGN**生效 |
| BSS | Basic Service Set,包括当前控制信道 、扩展信道（EXT20 EXT40）的AP统计情况，通过一个公式计算BSS因子的score，不是各个信道AP数的相加 |
|BUSY|参考 CCA类型 CCASTATS_OBSS统计,该统计数据包括当前信道和ext20信道|
|INTF|参考 CCA类型 CCASTATS_NOPKT统计,该统计数据包括当前信道和ext20信道|
|I-ADJ|参考 CCA类型 CCASTATS_NOPKT统计.该统计数据包括非当前信道、非ext20信道的统计加权值.权重由当前信道和相关信道的距离确定|
|FCS|参考 CCA类型 CCASTATS_NOCTG统计.该统计数据包括当前信道和ext20信道之和，NOCTG统计指代收到CRC校验错误的数据包|
|TXPWR|目前没有实现|
|NOISE|指代大于-95db background noise. 计算当前信道、ext20信道的background noise得分|
|CNS|CNS(Composite Noise Score) : **bgnoise + txop_noise.**  *txop_noise = 0; when chan_idle > 40; txop_noise =int[ (chan_idle – 40)4 / 51200];  0 <=  chan_idle <= 4* |
|ADJ|adjacent bss, 就是指代某些相邻信道的AP数量（特定相邻信道，例如20MHz的带宽信道，那么相邻信道就是指和它们相差20Mhz的信道|
|Total|就是指各个因子score的加权之和|

## 2.3 信道选择主流程

**ACS的相关参数**

acs_ifnames：wl接口，acsd进行信道选择的接口

acs_cs_scan_timer：信道选择的定时器。但有STA关联时，不会进行自动信道选择

acs_tp_restrict：该值为1时，自动信道不选择12，13

acs_2g_ch_no_restrict：该值为0时，仅从1，6，11中选择，仅对2.4G有效

acs_fcs_mode：未定义acs_policy情况下，该值为1，使用FCS策略；否则使用Default策略

acs_policy: 信道策略选择

acs_excl_chans：这个列表中的信道不会被选择

acs_ctrl_chan_adjust： 是否允许改变主控信道

**无效信道标志**

```C
#define ACS_INVALID_COEX	0x1
#define ACS_INVALID_INTF_CCA	0x2
#define ACS_INVALID_INTF_BGN	0x4
#define ACS_INVALID_OVLP	0x8
#define ACS_INVALID_NOISE	0x10
#define ACS_INVALID_ALIGN	0x20
#define ACS_INVALID_144		0x40
#define ACS_INVALID_DFS		0x80
#define ACS_INVALID_CHAN_FLOP_PERIOD	0x100
#define ACS_INVALID_EXCL		0x200
#define ACS_INVALID_MISMATCH_SB		0x400
#define ACS_INVALID_SAMECHAN		0x800
#define ACS_INVALID_DFS_NO_11H		0x1000	/* Cannot use DFS channels if 802.11h is off */
#define ACS_INVALID_LPCHAN              0x2000
````
(1) ACS_INVALID_COEX: 仅仅适用于2.4G 40Mhz, 这里说明非COEX的情况： Existing BSS is ONLY okay if:Our control channel is aligned with existing 20 or Control Channel，Our extension channel is aligned with an existing extension channel.

(2) ACS_INVALID_INTF_CCA: 超过CCA的门限阈值，参考前面intf因子说明

(3) ACS_INVALID_INTF_BGN: 超过background noise threshold, 大于-65dBm

(4) ACS_INVALID_OVLP: 信道没有问题，但不是1,6,11信道中的一个，仅对2.4G有效

(5) ACS_INVALID_NOISE: 信道噪声很大，具体参数函数acs_remove_noisy_cns

(6) ACS_INVALID_ALIGN: 非对齐情况；对于20MHz信道带宽，不存在该情况；对于40Mhz或80Mhz信道带宽，在主信道中，存在有AP（40MHz或80MHz带宽）使用该信道扩展信道的情况，或者在扩展信道里，存在AP使用该扩展信道作为主信道的情况

(7) ACS_INVALID_144: ban channel 144 as primary or secondary 20,不允许使用该信道

(8) ACS_INVALID_DFS: 在不允许使用DFS信道情况下，标志DFS信道

(9) ACS_INVALID_CHAN_FLOP_PERIOD：avoid channel flip and flop, skip the channel which selected in some amount of time

(10) ACS_INVALID_EXCL: Invalidate all channels from selection present in Exclusion list，if present in FCS configuration

(11) ACS_INVALID_MISMATCH_SB ：Check for same sideband

(12) ACS_INVALID_SAMECHAN: 信道上次使用过

(13) ACS_INVALID_DFS_NO_11H: Cannot use DFS channels if 802.11h is off

(14) ACS_INVALID_LPCHAN： 用于5G，如果高功率信道可使用，那么把低功率信道标志，低功率信道范围是 < 60

**信道选择主流程**

![Alt text](imgs/acs_channel/ACS_main_process.jpg)

![Alt text](imgs/acs_channel/acs_select_chanspec.png)

Note: 

（1）冲突：这里是指COEX问题

（2）排除相关信道：根据实际情况（例如2.4G不允许使用非1,6,11信道; 不允许使用DFS信道）排除相关信道

（3）acs_candidate_score: 在计算各个信道的不同因子score，也会把相关信道设为invalid，例如非对齐情况

**一个例子：** 下图打印了2.5G和5G的ACS结果，图中显示的无效信道的无线类型，以及有效信道的得分情况。

![Alt text](imgs/acs_channel/channel_select_candidate.png)


# 三 信道选择相关问题

**(1) 合适信道选择策略**

不同的策略适应的场景可以不同，因此没有最好的策略。目前BCM推荐适应于大部分场景的策略： 2.4G选择Optimized，5G选择FCS策略.

![Alt text](imgs/acs_channel/selected_policy.png)

以下是2.4G，针对以上3个策略的测试。从测试结果来看，optimized和FCS的效果差异不大，default的效果最差。

![Alt text](imgs/acs_channel/channel_select_test.JPG)

**(2) 2.4G里 信道自动选择是否应该仅选择1,6,11信道**

此3信道在20MHz时时不会相互干扰，不会产生邻频干扰。如果所有modem都这样选择挺好。目前来看，其他厂商不一定使用此方式，因此较好的方式是1～11信道都可以自由选择。

**(3) 2.4G里 信道自动选择是否应该选择12,13信道**

目前不是所有国家都支持12～13信道，例如美国仅有1～11信道。虽然在某国支持12～13信道，但是不能防止有美国标准的设备在某国使用，因此不应该选择12～13信道

**(4) 当存在STA关联时，信道选择是否需要定时工作, 定时间隔如何确定**

在有STA关联时，不应该切换信道。如果AP不支持信道宣告，那么STA必然无法及时知道新信道；就算AP支持信道宣告，也无法保证STA能否跟随AP切换到相应信道，可能使的STA关联到其他信道；实验得知，在有数据通信新切换信道，可能会导致STA的状态发生异常，从而使得需要手动进行重新关联；此外，对于实时的服务（例如语音、视频），会产生不友好的体验。

**(5)信道自动选择是否需要设计优先选择的信道**

根据实际情况，可以设计优先选择的信道，例如5G Band1和Band4可以同时使用时，如果Band4对功率没有限制，那么可以优先从Band4信道中选择

**(6)是否需要手动设置带宽/信道（手动设置带宽/信道是否有效）**

没有必要的话，不要手动设置带宽或信道。如手动选择大带宽，那么可能导致在大带宽情况下，不存在有效信道，仅能使用默认信道的情况，其实这种情况下可能效果更差

**(7)当信道带宽为40Mhz、80Mhz时，是否可以自动选择主控信道（acs_adjust_ctrl_chan，40Mhz时，4L和 8U是一样的信道范围）**

例如，ACS选择4L信道，但是发现8U信道存在更多的AP，在*acs_adjust_ctrl_chan=1*时，ACS可以把主控信道设置为8,此时可以监视到更多AP的行为。如果*acs_adjust_ctrl_chan=0*那么就不会做如此操作。
为了wifi性能更好，在带宽/信道都是auto时，应该使得*acs_adjust_ctrl_chan=1*