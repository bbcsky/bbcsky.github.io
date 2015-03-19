
### php生成PDF的类mPDF

由于公司业务的需要,最近需要把html静态文件生成pdf,在网上找了很多类文件来实现,效果都不是很好.最先用的是tcpdf这个类特别的慢,而且当前版本有一个很让人头疼的问题—css中的背景图片无法获取到,找了很多资料都无法解决.最后发现mpdf可能实现该功能,而且效率也比tcpdf快.

mpdf的官方下载地址:http://www.mpdf1.com/mpdf/download

下载后里面有实例,可以参照着做一个就知道了.当然官方网站也有实例,网址:http://mpdf1.com/common/mpdf/examples/

官方提供的文档:http://mpdf1.com/manual/

虽然文档全是英文的,但咬咬牙也还是可以看懂的.下面把我自己写的一个例子贡献给大家

```php
<?php
/*
*功能:生成用户诊断报告PDF文件
*创建时间:2013-09-12
*/
//phpinfo();exit;
//引入MPDF类文件
set_time_limit(0);
include ‘/include/MPDF57/mpdf.php';

//实例化mpdf
$mpdf=new mPDF(‘utf-8′,’A4′,”,’宋体’,0,0,20,10);

//设置字体,解决中文乱码
$mpdf->useAdobeCJK = true;
$mpdf->SetAutoFont(AUTOFONT_ALL);

//获取要生成的静态文件
$html=file_get_contents(‘template.html’);

echo $html;exit;

//设置PDF页眉内容
$header='<table width=”95%” style=”margin:0 auto;border-bottom: 1px solid #4F81BD; vertical-align: middle; font-family:
serif; font-size: 9pt; color: #000088;”><tr>
<td width=”10%”></td>
<td width=”80%” align=”center” style=”font-size:16px;color:#A0A0A0″>页眉</td>
<td width=”10%” style=”text-align: right;”></td>
</tr></table>';

//设置PDF页脚内容
$footer='<table width=”100%” style=” vertical-align: bottom; font-family:
serif; font-size: 9pt; color: #000088;”><tr style=”height:30px”></tr><tr>
<td width=”10%”></td>
<td width=”80%” align=”center” style=”font-size:14px;color:#A0A0A0″>页脚</td>
<td width=”10%” style=”text-align: left;”>页码：{PAGENO}/{nb}</td>
</tr></table>';

//添加页眉和页脚到pdf中
$mpdf->SetHTMLHeader($header);
$mpdf->SetHTMLFooter($footer);

//设置pdf显示方式
$mpdf->SetDisplayMode(‘fullpage’);

//设置pdf的尺寸为270mm*397mm
//$mpdf->WriteHTML(‘<pagebreak sheet-size=”270mm 397mm” />’);

//创建pdf文件
$mpdf->WriteHTML($html);

//删除pdf第一页(由于设置pdf尺寸导致多出了一页)
//$mpdf->DeletePages(1,1);

//输出pdf
$mpdf->Output();

exit;

?>
```
