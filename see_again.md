A Example about applying **`m_mhw`** to real-world data
==================================================================

We provide an example about applying **`m_mhw`** to real-world data. In this example, we use **`m_mhw`** to detect and analyze MHWs off eastern Tasmania during 1982 - 2016.

Loading data
-------------

Firstly, Let’s load NOAA OI SST data (Reynolds et al., 2007). Due to the limitation of file size in Github, these data are stored in folder `data` for one year per file. We need to reconstruct these data into one combined dataset.

```
sst_full=NaN(32,32,datenum(2016,12,31)-datenum(1982,1,1)+1);
for i=1982:2016;
    file_here=['sst_' num2str(i)];
    load(file_here);
    eval(['data_here=sst_' num2str(i) ‘;’])
    sst_full(:,:,(datenum(i,1,1):datenum(i,12,31))-datenum(1982,1,1)+1)=data_here;
end
```

The `sst_full` contains SST in [147-155E, 45-37S] in resolution of 0.25 from 1982 to 2016.

```
size(sst_full); %size of data
datenum(2016,12,31)-datenum(1982,1,1)+1 % The temporal length is 35 years.
load(‘sst_loc’);% also load lon location and lat location

```

Detecting MHWs and MCSs
-------------

In this section we detect MHWs and MCSs off eastern Tasmania based on definition give by Hobday et al. (2016) and Schlegel et al. (2017). The climatology and thresholds (90th and 10th percentile) are calculated for SST during 1982 - 2005.

```
% Here we detect marine heatwaves off eastern Tasmania based on the
% traditional definition of MHWs (Hobday et al. 2016). We detected MHWs
% during 1993 to 2016 for climatologies and thresholds in 1982 to 2005.

[MHW,mclim,m90,mhw_ts]=detect(sst_full,1982,2005,1982,1993,2016); %take about 30 seconds.

% Additionally, we also detect MCSs during 1982 to 2005 based on the same
% climatologies. 

[MCS,~,m10,mcs_ts]=detect(sst_full,1982,2005,1982,1993,2016,'Event','MCS','Threshold',0.1);
```

Let’s have a look at these two resultant data (`MHW` and `MCS`).

```
MHW(1:5,:)
MCS(1:5,:)
```

The properties `mhw_onset` and `mhw_end`are in a strange format. This is due to the fact that they are originally constructed in numeric format. We could change it to date format by following steps.

```
datevec(num2str(MHW{1:5,:}),'yyyymmdd') % vector
datestr(datevec(num2str(MHW{1:5,:}),'yyyymmdd')) % string
datenum(num2str(MHW{1:5,:}),'yyyymmdd') % number
```

Visualizing MHW/MCS time series
-------------

In this section we use `event_line` to visualize MHW/MCS events in grid (1,2). Firstly, let’s have a look of MHW time series during Sep 2015 to Apr 2016 in this loc.

```
% Have a look of MHW events in grid (1,2) from Sep 2015 to Apr 2016
figure('pos',[10 10 1000 1000]);
event_line(sst_full,MHW,mclim,m90,[1 2],1982,[2015 9 1],[2016 5 1]);
```

<div>
<center>
<img src=“see/store_figure/event_mhw.png” width="60%">
</div>
