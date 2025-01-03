本小应用旨在帮助自动化生成moviepilot中全条件的优先级规则组排序  
官方tag： 官方原本的tag，可供快速复制  
规则组代码 即规则id，支持自定义规则   
权重 权重推荐按如下设置以二为倍数设置阶梯，值越大越重要；同组权重以最高权重为基准递减；  
不同级别分组之间，低权重分组的递减值 应该超过 高权重封组的最大递减值。  

设置好代码、权重和分组情况后，点击排列即生成全排列，并可以删除其中某几项来简化排列。  
最后点击复制即将代码复制到剪贴板，导入mp使用。  

exp： 以下仅以分辨率，字幕为例，讲解推荐权重设置。  
假设需要分辨率比字幕重要，我可以将分辨率中的最高级 4k 权重设置为2，字幕中的最高级 SPECSUB 权重设置为1  
分组 同一分组中的选项只会选取一次。  
所以说分辨率中的 4k 1080p 720p应该设置为同组、SPESCUB 和 CNSUB 设置为另一组。  
则高权重组递减值设为0.1，低权重组递减这为0.3。  
则4k 2、1080p 1.9、720p 1.8、SPESCUB 1、CNSUB 0.7。  
如此生成的全排列即符合大部分人直觉需求。  

本程序会在运行目录生成配置文件，自动记录已填写的规则组代码以及权重，和分组情况。  
所以也可以直接复制已有的配置文件，来参考别人的配置，仓库有个默认参考default.ini。   

补充 4个有用的自定义规则  
HQ  
包含 HQ|高码|EDR  
作用 选取高码率  

DynaHDR  
包含 hdr10\+[\s.]+|(?=.*(Dolby[\s.]+Vision|DOVI|[\s.]+DV[\s.]+|杜比视界))(?=.*hdr)  
作用 选取兼容的动态元数据的hdr资源（[杜比视界+hdr]和“hdr10+”）  
 
filterOriginal  
排除 ^(?!.*remux)(?=.*(diy|原盘))  
作用 替代官方的“bluray”原盘，优化命中逻辑，减少误命中的情况  

hdrx
包含 hdr[\s.]+
作用 代替官方的hdr，我感觉这样简化一点就够了，加快点速度

onlydv
排除 (?=.*(Dolby[\s.]+Vision|DOVI|[\s.]+DV[\s.]+|杜比视界))(^(?!.*HDR)|(?=.*p7|双层))
作用 就是筛选出纯dv，不兼容会发紫的那个。以及双层杜比也排除掉了，因为我也没有蓝光机，如果不需要的话把最后|后面的删掉。

qiyi
包含 (7)[\s.]+(1)
作用 7.1多声道

wuyi
包含 (5)[\s.]+(1)
作用 5.1多声道

个人在使用的规则，没有做分类，只是全局的。  
自定义规则  
[{"id":"filterGlobal","name":"filterGlobal","include":"","exclude":"无字|先行|BDMV|RMVB|vcd|480p|枪版|hami|TC|TrueHd","seeders":"1"},{"id":"HQ","name":"HQ","include":"HQ|高码|EDR","exclude":"","size_range":""},{"id":"filteronlydv","name":"filteronlydv","include":"","exclude":"(?=.*(Dolby[\\s.]+Vision|DOVI|[\\s.]+DV[\\s.]+|杜比视界))(^(?!.*HDR)|(?=.*p7|双层))"},{"id":"DynaHDR","name":"动态hdr","include":"hdr10\\+[\\s.]+|(?=.*(Dolby[\\s.]+Vision|DOVI|[\\s.]+DV[\\s.]+|杜比视界))(?=.*hdr)","exclude":""},{"id":"filterOriginal","name":"filterOriginal","include":"","exclude":"^(?!.*remux)(?=.*(diy|原盘))"},{"id":"hdrx","name":"hdrx","include":"hdr[\\s.]+","exclude":""},{"id":"qiyi","name":"7.1","include":"(7)[\\s.]+(1)"},{"id":"wuyi","name":"5.1","include":"(5)[\\s.](1)"}]

优先级规则组  
[{"name":"前置排除","rule_string":"filterOriginal&filteronlydv&filterGlobal& !3D ","media_type":"","category":""},{"name":"4k洗版","rule_string":" 4K ","media_type":"","category":""},{"name":"下载规则组","rule_string":" SPECSUB & DynaHDR & qiyi > DynaHDR & qiyi > SPECSUB & DynaHDR & wuyi > DynaHDR & wuyi > SPECSUB & hdrx & qiyi > hdrx & qiyi > SPECSUB & hdrx & wuyi > hdrx & wuyi > SPECSUB & qiyi > qiyi > SPECSUB & wuyi > wuyi > SPECSUB & DynaHDR > DynaHDR > SPECSUB & hdrx > hdrx > SPECSUB > !3D ","media_type":"","category":""}]

说明，先前置排除一波(包括了杜比无损全景声，这个也要求蓝光机)，然后4k洗版是用来给洗版加一个4k要求，下载规则组实现的是 多声道 优先级大于 杜比等hdr  大于 特效字幕。
