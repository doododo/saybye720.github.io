---
layout:		post
title:		ThinkPHP开发过程问题总结
description: ThinkPHP是一个免费开源的，快速、简单的面向对象的轻量级PHP 开发框架，任何框架不存在好坏，只能是对项目来说满足即可。国产框架比较适合中国人思想，文档比较多，简单易学。
category:	blog
---

## 查询条件不等于某个值

在SQL语句中，我们的查询条件可以直接 `id<>1`，想要在thinkphp中实现此查询有2中方法

```
where("id<>1");
// 或者
$map['id']=array('neq',1);
```

* 举一反三  neq可以换成 gt like  in between等 *

## trace不起作用

必须要输出到模板，才会有trace信息

## 都检查了，都没有问题，还是出错

先吧Runtime目录下的缓存文件都清空了先。不要轻信说明的debug开启了就不产生缓存，手动清空最给力。

## 更新同字段名的多条记录

用 `$_post['字段名']` 将得刡一个数组，然后循环更新，参照代码

```
    $M = M("Config");
    for($i = 0;$i < count($_POST["id"]); $i++) {
        $data["id"] = $_POST["id"][$i];
        $data["body"] = $_POST["body"][$i];
        $M->save($data);
    }
```

## 使用create()隐藏数据库中的字段名

```
class UserModel extends Model{
	protected $_map = array(
		'name' => 'username',
		'mail' => 'email',
	);
}
```

## 移动端访问自动切换主题模板

1.将 `ismobile()` 加入到｛项目`/Common/common.php`｝

```
	function ismobile() {
	    // 如果有HTTP_X_WAP_PROFILE则一定是移动设备
	    if (isset ($_SERVER['HTTP_X_WAP_PROFILE']))
	        return true;

	    //此条摘自TPM智能切换模板引擎，适合TPM开发
	    if(isset ($_SERVER['HTTP_CLIENT']) &&'PhoneClient'==$_SERVER['HTTP_CLIENT'])
	        return true;
	    //如果via信息含有wap则一定是移动设备,部分服务商会屏蔽该信息
	    if (isset ($_SERVER['HTTP_VIA']))
	        //找不到为flase,否则为true
	        return stristr($_SERVER['HTTP_VIA'], 'wap') ? true : false;
	    //判断手机发送的客户端标志,兼容性有待提高
	    if (isset ($_SERVER['HTTP_USER_AGENT'])) {
	        $clientkeywords = array(
	            'nokia','sony','ericsson','mot','samsung','htc','sgh','lg','sharp','sie-','philips','panasonic','alcatel','lenovo','iphone','ipod','blackberry','meizu','android','netfront','symbian','ucweb','windowsce','palm','operamini','operamobi','openwave','nexusone','cldc','midp','wap','mobile'
	        );
	        //从HTTP_USER_AGENT中查找手机浏览器的关键字
	        if (preg_match("/(" . implode('|', $clientkeywords) . ")/i", strtolower($_SERVER['HTTP_USER_AGENT']))) {
	            return true;
	        }
	    }
	    //协议法，因为有可能不准确，放到最后判断
	    if (isset ($_SERVER['HTTP_ACCEPT'])) {
	        // 如果只支持wml并且不支持html那一定是移动设备
	        // 如果支持wml和html但是wml在html之前则是移动设备
	        if ((strpos($_SERVER['HTTP_ACCEPT'], 'vnd.wap.wml') !== false) && (strpos($_SERVER['HTTP_ACCEPT'], 'text/html') === false || (strpos($_SERVER['HTTP_ACCEPT'], 'vnd.wap.wml') < strpos($_SERVER['HTTP_ACCEPT'], 'text/html')))) {
	            return true;
	        }
	    }
	    return false;
	 }
```

2.在｛项目/Lib/｝创建一个 CommonAction.php，如果你的项目已公共控制器，则无需创建，直接加在里面即可。

 ```
	 Class CommonAction extends Action{
	    Public function _initialize(){
	        //移动设备浏览，则切换模板
	        if (ismobile()) {
	            //设置默认默认主题为 Mobile
	            C('DEFAULT_THEME','Mobile');
	        }
	        //TODO
	    }
	 }
```

## 实现excel数据的导入导出

1.在[http://phpexcel.codeplex.com/][1]下载最新PHPExcel放到Vendor下，注意位置：

`ThinkPHP\Extend\Vendor\PHPExcel\PHPExcel.php。`

2.导出excel代码实现

```
	public function  index(){
	    $this->display();
	}

	public function exportExcel($expTitle,$expCellName,$expTableData){
	    $xlsTitle = iconv('utf-8', 'gb2312', $expTitle);//文件名称
	    $fileName = $_SESSION['account'].date('_YmdHis');//or $xlsTitle 文件名称可根据自己情况设定
	    $cellNum = count($expCellName);
	    $dataNum = count($expTableData);
	    vendor("PHPExcel.PHPExcel");

	    $objPHPExcel = new PHPExcel();
	    $cellName = array('A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD','AE','AF','AG','AH','AI','AJ','AK','AL','AM','AN','AO','AP','AQ','AR','AS','AT','AU','AV','AW','AX','AY','AZ');

	    $objPHPExcel->getActiveSheet(0)->mergeCells('A1:'.$cellName[$cellNum-1].'1');//合并单元格
	   // $objPHPExcel->setActiveSheetIndex(0)->setCellValue('A1', $expTitle.'  Export time:'.date('Y-m-d H:i:s'));
	    for($i=0;$i<$cellNum;$i++){
	        $objPHPExcel->setActiveSheetIndex(0)->setCellValue($cellName[$i].'2', $expCellName[$i][1]);
	    }
	      // Miscellaneous glyphs, UTF-8
	    for($i=0;$i<$dataNum;$i++){
	      for($j=0;$j<$cellNum;$j++){
	        $objPHPExcel->getActiveSheet(0)->setCellValue($cellName[$j].($i+3), $expTableData[$i][$expCellName[$j][0]]);
	      }
	    }

	    header('pragma:public');
	    header('Content-type:application/vnd.ms-excel;charset=utf-8;name="'.$xlsTitle.'.xls"');
	    header("Content-Disposition:attachment;filename=$fileName.xls");//attachment新窗口打印inline本窗口打印
	    $objWriter = PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel5');
	    $objWriter->save('php://output');
	    exit;
	}
	/**
	 *
	 * 导出Excel
	 */
	function expUser(){//导出Excel
	    $xlsName  = "User";
	    $xlsCell  = array(
	    array('id','账号序列'),
	    array('truename','名字'),
	    array('sex','性别'),
	    array('res_id','院系'),
	    array('sp_id','专业'),
	    array('class','班级'),
	    array('year','毕业时间'),
	    array('city','所在地'),
	    array('company','单位'),
	    array('zhicheng','职称'),
	    array('zhiwu','职务'),
	    array('jibie','级别'),
	    array('tel','电话'),
	    array('qq','qq'),
	    array('email','邮箱'),
	    array('honor','荣誉'),
	    array('remark','备注')
	    );
	    $xlsModel = M('Member');

	    $xlsData  = $xlsModel->Field('id,truename,sex,res_id,sp_id,class,year,city,company,zhicheng,zhiwu,jibie,tel,qq,email,honor,remark')->select();
	    foreach ($xlsData as $k => $v)
	    {
	        $xlsData[$k]['sex']=$v['sex']==1?'男':'女';
	    }
	    $this->exportExcel($xlsName,$xlsCell,$xlsData);

	}
```

3.导入excel数据代码

```
	function impUser(){
	    if (!empty($_FILES)) {
	        import("@.ORG.UploadFile");
	        $config=array(
	            'allowExts'=>array('xlsx','xls'),
	            'savePath'=>'./Public/upload/',
	            'saveRule'=>'time',
	        );
	        $upload = new UploadFile($config);
	        if (!$upload->upload()) {
	            $this->error($upload->getErrorMsg());
	        } else {
	            $info = $upload->getUploadFileInfo();

	        }

	        vendor("PHPExcel.PHPExcel");
	            $file_name=$info[0]['savepath'].$info[0]['savename'];
	            $objReader = PHPExcel_IOFactory::createReader('Excel5');
	            $objPHPExcel = $objReader->load($file_name,$encode='utf-8');
	            $sheet = $objPHPExcel->getSheet(0);
	            $highestRow = $sheet->getHighestRow(); // 取得总行数
	            $highestColumn = $sheet->getHighestColumn(); // 取得总列数
	            for($i=3;$i<=$highestRow;$i++)
	            {
	               $data['account']= $data['truename'] = $objPHPExcel->getActiveSheet()->getCell("B".$i)->getValue();
	                $sex = $objPHPExcel->getActiveSheet()->getCell("C".$i)->getValue();
	               // $data['res_id']    = $objPHPExcel->getActiveSheet()->getCell("D".$i)->getValue();
	                $data['class'] = $objPHPExcel->getActiveSheet()->getCell("E".$i)->getValue();
	                $data['year'] = $objPHPExcel->getActiveSheet()->getCell("F".$i)->getValue();
	                $data['city']= $objPHPExcel->getActiveSheet()->getCell("G".$i)->getValue();
	                $data['company']= $objPHPExcel->getActiveSheet()->getCell("H".$i)->getValue();
	                $data['zhicheng']= $objPHPExcel->getActiveSheet()->getCell("I".$i)->getValue();
	                $data['zhiwu']= $objPHPExcel->getActiveSheet()->getCell("J".$i)->getValue();
	                $data['jibie']= $objPHPExcel->getActiveSheet()->getCell("K".$i)->getValue();
	                $data['honor']= $objPHPExcel->getActiveSheet()->getCell("L".$i)->getValue();
	                $data['tel']= $objPHPExcel->getActiveSheet()->getCell("M".$i)->getValue();
	                $data['qq']= $objPHPExcel->getActiveSheet()->getCell("N".$i)->getValue();
	                $data['email']= $objPHPExcel->getActiveSheet()->getCell("O".$i)->getValue();
	                $data['remark']= $objPHPExcel->getActiveSheet()->getCell("P".$i)->getValue();
	                $data['sex']=$sex=='男'?1:0;
	                $data['res_id'] =1;

	                $data['last_login_time']=0;
	                $data['create_time']=$data['last_login_ip']=$_SERVER['REMOTE_ADDR'];
	                $data['login_count']=0;
	                $data['join']=0;
	                $data['avatar']='';
	                $data['password']=md5('123456');
	                M('Member')->add($data);
	            }
	             $this->success('导入成功！');
	    }else
	        {
	            $this->error("请选择上传的文件");
	        }
	}
```

4.模板代码

```
	<html>
	<head>

	</head>
	<body>
	<P><a href="{:U('Index/expUser')}" >导出数据并生成excel</a></P><br/>
	    <form action="{:U('Index/impUser')}" method="post" enctype="multipart/form-data">
	        <input type="file" name="import"/>
	        <input type="hidden" name="table" value="tablename"/>
	        <input type="submit" value="导入"/>
	    </form>
	</body>

	</html>
```

## 总结

2010年开始接触ThinkPHP，遇到了不少问题暂时先整理这些吧。开发些小网站ThinkPHP还是蛮不错的，文档多，上手快，基本遇到的问题都能搜到。鄙人很讨厌那种技术水平一般，天天说这框架垃圾，那框架垃圾的人，每个框架都有优缺点，像人一样，你是一个Java Developer难道就瞧不起Javascript.....不多说了，好好学习吧。


[1]: http://phpexcel.codeplex.com/
