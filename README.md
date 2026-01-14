2026.1.15
1.因为mp的过滤性能大幅提升，更新了我的优先级规则配置，将HQ加入排序。若电脑性能够强，可以自行用本软件继续编麻花。
2.更改了权重推荐以及说明。

官方tag： 官方原本的tag，可供快速复制  
规则组代码 即规则id，支持自定义规则   
权重 权重推荐按如下设置以二为倍数设置阶梯，值越大越重要；同组权重以最高权重为基准递减；  
不同级别分组之间，低权重分组的递减值 应该超过 高权重封组的最大递减值。  

设置好代码、权重和分组情况后，点击排列即生成全排列，并可以删除其中某几项来简化排列。  
最后点击复制即将代码复制到剪贴板，导入mp使用。  

exp： 以下仅以分辨率，字幕为例，讲解推荐常规权重设置。
应该从低到高配置，确保优先项比所有可选低权重之和都要高。（即比同阶次权重以及其余低阶的最高权重项的权重和高）
假设需要分辨率比字幕重要，我可以分辨率中的 4k 1080p 720p应该设置为同组、SPESCUB 和 CNSUB 设置为另一组。  
分组 同一分组中的选项只会选取一次。  
比如先将字幕中的最低级 CNSUB 权重设置为0.1，较高级 SPECSUB 权重设置为0.2 。  
分辨率中的最低级 720P 权重设置为1，更高级1080P 权重设置为 1.4（>1+0.2）, 更高级 2K 权重设置为 1.7(>1.4+0.2)
如此生成的全排列即符合大部分人直觉需求。  

本程序会在运行目录生成配置文件，自动记录已填写的规则组代码以及权重，和分组情况。  
所以也可以直接复制已有的配置文件，来参考别人的配置，仓库有个默认参考default.ini。   

补充 4个有用的自定义规则  
HQ  
包含 HQ|高码|EDR  
作用 选取高码率  

DynaHDR  
包含 `hdr10\+[\s.]+|(?=.*(Dolby[\s.]+Vision|DOVI|[\s.]+DV[\s.]+|杜比视界))(?=.*hdr)`  
作用 选取兼容的动态元数据的hdr资源（[杜比视界+hdr]和“hdr10+”）  
 
filterOriginal  
排除 `^(?!.*remux)(?=.*(diy|原盘))`  
作用 替代官方的“bluray”原盘，优化命中逻辑，减少误命中的情况  

hdrx
包含 `hdr[\s.]+`  
作用 代替官方的hdr，我感觉这样简化一点就够了，加快点速度

onlydv
排除 `(?=.*(Dolby[\s.]+Vision|DOVI|[\s.]+DV[\s.]+|杜比视界))(^(?!.*HDR)|(?=.*p7|双层))`  
作用 就是筛选出纯dv，不兼容会发紫的那个。以及双层杜比也排除掉了，因为我也没有蓝光机，如果不需要的话把最后|后面的删掉。

qiyi  
包含 `7[\s.]1[\s.-]`  
作用 7.1多声道  

wuyi  
包含 `5[\s.]1[\s.-]`  
作用 5.1多声道  

个人在使用的规则，没有做分类，只是全局的。  
自定义规则  
`[{"id":"filterGlobal","name":"filterGlobal","include":"","exclude":"无字|先行|BDMV|RMVB|vcd|480p|枪版|hami|TC|TrueHd","seeders":"1"},{"id":"HQ","name":"HQ","include":"HQ|高码|EDR","exclude":"","size_range":""},{"id":"filteronlydv","name":"filteronlydv","include":"","exclude":"(?=.*(Dolby[\\s.]+Vision|DOVI|[\\s.]+DV[\\s.]+|杜比视界))(^(?!.*HDR)|(?=.*p7|双层))"},{"id":"DynaHDR","name":"动态hdr","include":"hdr10\\+[\\s.]+|(?=.*(Dolby[\\s.]+Vision|DOVI|[\\s.]+DV[\\s.]+|杜比视界))(?=.*hdr)","exclude":""},{"id":"filterOriginal","name":"filterOriginal","include":"","exclude":"^(?!.*remux)(?=.*(diy|原盘))"},{"id":"hdrx","name":"hdrx","include":"hdr[\\s.]+","exclude":""},{"id":"qiyi","name":"7.1","include":"7[\\s.]1[\\s.-]?"},{"id":"wuyi","name":"5.1","include":"5[\\s.]1[\\s.-]"}]`

优先级规则组  
`[{"name":"前置排除","rule_string":"filterOriginal&filteronlydv&filterGlobal","media_type":"","category":""},{"name":"4k洗版","rule_string":" 4K ","media_type":"","category":""},{"name":"下载规则组","rule_string":" SPECSUB & HQ & DynaHDR & qiyi > HQ & DynaHDR & qiyi > SPECSUB & DynaHDR & qiyi > DynaHDR & qiyi > SPECSUB & HQ & hdrx & qiyi > HQ & hdrx & qiyi > SPECSUB & hdrx & qiyi > hdrx & qiyi > SPECSUB & HQ & qiyi > HQ & qiyi > SPECSUB & qiyi > qiyi > SPECSUB & HQ & DynaHDR & wuyi > HQ & DynaHDR & wuyi > SPECSUB & DynaHDR & wuyi > DynaHDR & wuyi > SPECSUB & HQ & hdrx & wuyi > HQ & hdrx & wuyi > SPECSUB & hdrx & wuyi > hdrx & wuyi > SPECSUB & HQ & wuyi > HQ & wuyi > SPECSUB & wuyi > wuyi > SPECSUB & HQ & DynaHDR > HQ & DynaHDR > SPECSUB & DynaHDR > DynaHDR > SPECSUB & HQ & hdrx > HQ & hdrx > SPECSUB & hdrx > hdrx > SPECSUB & HQ > HQ > SPECSUB > !3D ","media_type":"","category":""}]`

说明，先前置排除一波，然后4k洗版是用来给洗版加一个4k要求，下载规则组实现的是 多声道 优先级大于 杜比等hdr  大于 特效字幕。
