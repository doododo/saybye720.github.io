---
layout:		post
title:		PHP开发App接口之数据转换
category:	blog
description:	APP接口是供客户端通信用的，客户端APP可以通过接口获取数据和提交数据等操作。做APP接口需要考虑的问题：1、接口地址 2、接口数据格式 3、接口安全认证 4、接口分层
---

## 通信数据格式xml和json的区别、

XML可读性好，json数据生成和传送速度好。

## App几个必用的接口

1.版本升级接口

json_encode只能接收UTF-8格式数据，如果传输其他数据则返回null。

## 封装通讯数据接口标准格式

code：状态码
message： 提示信息（例如邮箱格式不正确，数据返回成功等）
data： 返回数据

## 写一个封装xml的方法

	/**
	 * 按xml格式输出通讯数据
	 * @param integer $code 状态码
	 * @param string $message 提示信息
	 * @param array $data 数据
	 * @return string
	 */
	public static function xmlEncode($code, $message, $data = array()){
        if(!is_numeric($code)){
            return '';
        }
        $result = array(
            'code' => $code,
            'message' => $message,
            'data' => $data,
        );
        header("Content-Type:text/xml");
        $xml = "<?xml version='1.0' encoding='UTF-8'?>";
        $xml .="<root>";
        $xml .=self::arrayToXml($data);
        $xml .="</root>";
        echo $xml;
    }

    /**
     * 把数组转换为xml
     * @param array $data 数组
     * @return string
     */
    public static function arrayToXml($data){
        $xml = $attr = "";
        foreach($data as $k => $v){
            if (is_numeric($k)) {
                $attr = " id='{$k}'";
                $k = "item";
            }
            $xml .= "<{$k}{$attr}>";
            $xml .= is_array($v) ? self::arrayToXml($v) : $v;
            $xml .= "</{$k}>";
        }
        return $xml;
    }
    //测试下
    apiTest::xmlEncode(200,"输出信息", ['id' => '100', 'name' => 'songqijian', 'type' => ['shuxue' => '100', 'yingyu' => '120'], 'num' => [1, 1,2], [1,444,666]]);
    //输出结果
    <root><id>100</id><name>songqijian</name><type><shuxue>100</shuxue><yingyu>120</yingyu></type><num><item id="0">1</item><item id="1">1</item><item id="2">2</item></num><item id="0"><item id="0">1</item><item id="1">444</item><item id="2">666</item></item></root>

