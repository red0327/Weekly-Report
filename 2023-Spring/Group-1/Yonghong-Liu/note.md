# Weekly Report
## 1.窃听场景建模
在原有的OTFS调制代码基础上，增加窃听信道，窃听信道参数暂时设置和主信道参数获取方式一致
```
function [eve_taps,eve_delay_taps,eve_Doppler_taps,eve_chan_coef] = OTFS_channel_gen_eve(N,M)
eve_taps = 4;
eve_delay_taps = [0,1,2,3];
eve_Doppler_taps = [0,1,2,3];
pow_prof = (1/eve_taps)*(ones(1,eve_taps));   %每个信道功率均分
eve_chan_coef = sqrt(pow_prof).*(sqrt(1/2)*(randn(1,eve_taps)+1i*randn(1,eve_taps)));     %信道系数
end
```
窃听信道和主信道都是多径传输，因此通过for循环得到最后的接收信号。窃听信道处撰写的函数只需要返回SNR以计算保密率
```
function rge = OTFS_output_eve(N,M,eve_taps,eve_delay_taps,eve_Doppler_taps,eve_chan_coef,sigma_2,s)
L = max(eve_delay_taps);          %通过确定时延判断需要加入的循环前缀编码长度来消除ISI
s = [s(N*M-L+1:N*M);s];       %引入循环前缀编码，将发射信号的后缀复制到前面               
eve_chan = 0;          %考虑到多径传输，将有效信息初始化
for itao = 1:eve_taps
    eve_chan = eve_chan+eve_chan_coef(itao)*circshift([s.*exp(1j*2*pi/M *(-L:-L+length(s)-1)*eve_Doppler_taps(itao)/N).';zeros(eve_delay_taps(end),1)],eve_delay_taps(itao));   %多个信道有效信息之和
end
noise = sqrt(sigma_2/2)*(randn(size(eve_chan)) + 1i*randn(size(eve_chan)));
rge = (eve_chan'*eve_chan)/(noise'*noise);   %窃听信道SNR
end
```
由于窃听信道参数和主信道没有太大区别，并且信道增益都是随机生成，因此最后保密速率大于0合格的比例占50%。
