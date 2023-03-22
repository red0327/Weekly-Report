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
