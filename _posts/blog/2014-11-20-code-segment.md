---
layout: post
title: 记录一下常用的代码片段
description: 这段时间写了一些比较好玩的代码，由于时间比较急促，代码不太健壮，完善后后更新
category: blog
---

## 金额转换为大写汉字

```php
/**
 * 人民币小写转大写
 *
 * @param string $number      数值
 * @param string $intUnit     币种单位，默认"元"
 * @param bool   $isRound     是否对小数进行四舍五入
 * @param bool   $isExtraZero 是否对整数部分以0结尾，小数存在的数字附加0,比如1960.30，
 *                            有的需要输出"壹仟玖佰陆拾元零叁角"，实际上"壹仟玖佰陆拾元叁角"也是对的，随意，默认第一种
 * @return string
 */
public static function formatMoney($number, $intUnit = '元', $isRound = true, $isExtraZero = true)
{
    $money = $number / 100;
    $parts = explode('.', $money, 2);
    $int   = isset($parts[0]) ? strval($parts[0]) : '0';
    $dec   = isset($parts[1]) ? strval($parts[1]) : '';

    // 如果小数点后多于2位，不四舍五入就直接截，否则就处理
    $decLen = strlen($dec);

    if (isset($parts[1]) && $decLen > 2) {
        $dec = $isRound
                ? substr(strrchr(strval(round(floatval("0.".$dec), 2)), '.'), 1)
                : substr($parts[1], 0, 2);
    }

    // 当number为0.001时，小数点后的金额为0元
    if (empty($int) && empty($dec)) {
        return '零';
    }

    // 定义
    $chs     = ['0','壹','贰','叁','肆','伍','陆','柒','捌','玖'];
    $uni     = ['','拾','佰','仟'];
    $decUnit = ['角', '分'];
    $exp     = ['', '万'];
    $res     = '';

    // 整数部分从右向左找
    for ($i = strlen($int) - 1, $k = 0; $i >= 0; $k++) {
        $str = '';
        // 按照中文读写习惯，每4个字为一段进行转化，i一直在减
        for ($j = 0; $j < 4 && $i >= 0; $j++, $i--) {
            $u   = $int{$i} > 0 ? $uni[$j] : '';
            $str = $chs[$int{$i}] . $u . $str;
        }

        $str = rtrim($str, '0');
        $str = preg_replace("/0+/", "零", $str);
        if (!isset($exp[$k])) {
            $exp[$k] = $exp[$k - 2] . '亿';
        }
        $u2  = $str != '' ? $exp[$k] : '';
        $res = $str . $u2 . $res;
    }

    // 如果小数部分处理完之后是00，需要处理下
    $dec = rtrim($dec, '0');

    // 小数部分从左向右找
    if (!empty($dec)) {
        $res .= $intUnit;

        // 是否要在整数部分以0结尾的数字后附加0，有的系统有这要求
        if ($isExtraZero) {
            if (substr($int, -1) === '0') {
                $res.= '零';
            }
        }

        for ($i = 0, $cnt = strlen($dec); $i < $cnt; $i++) {
            $u = $dec{$i} > 0 ? $decUnit[$i] : '';
            $res .= $chs[$dec{$i}] . $u;
        }
        $res = rtrim($res, '0');
        $res = preg_replace("/0+/", "零", $res);
    } else {
        $res .= $intUnit . '整';
    }

    return $res;
}
```

## 按年月生成连续的流水号

```php
// migration文件
public function up()
{
    Schema::create('finance_generate_records', function (Blueprint $table) {
        $table->increments('id');
        $table->string('type')->default('')->comment('类型');
        $table->char('year', 4)->default('')->comment('年');
        $table->char('month', 2)->default('')->comment('月');
        $table->integer('number')->unsigned()->default(1)->comment('最后数字');
        $table->index(['type', 'year', 'month']);

        $table->timestamps();
    });

    \DB::statement("ALTER TABLE `finance_generate_records` comment '流水单号生成表'");
}
```

```php
/**
 * 按年月生成连续的流水号
 * 规则：类型的首字母大写+年月+0-10000直接的递增数，不足9999的前面补0
 *      如果开头字母
 *
 * @param $type   类型 (eg.首字母)
 * @param $length 末尾数字的数量 默认长度为4位（0~9999）
 * @return string
 */
public static function generateNo($type, $length = 4)
{
    $where      = [
        'type'  => $type,
        'year'  => date("Y"),
        'month' => date("m")
    ];
    $lastRecord = GenerateRecord::where($where)->orderBy('id', 'DESC')->first();
    $number     = count($lastRecord) ? $lastRecord->number + 1 : 1;

    // 此单据当月单号已经用完
    if ($number > (int) substr('9999999999999', $length)) {
        \Log::error('此单据当月单号已经用完');

        return ['code' => -1, 'message' => '此单据当月单号已经用完'];
    }
    $newNumber  = strtoupper($type) . date("Ym") . sprintf("%0{$length}d", $number);
    $data       = array_merge($where, ['number' => $number]);
    GenerateRecord::insert($data);

    return $newNumber;
}
```

*有心思可以把流水号存到Redis*