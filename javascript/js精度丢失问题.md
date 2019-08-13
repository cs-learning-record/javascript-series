![image](../img/timg.jpg)
<br>

## 前言

众所周知，JavaScript浮点数运算时经常遇到会`0.000000001`和`0.999999999`这样奇怪的结果，如`0.1+0.2=0.30000000000000004`、`1-0.9=0.09999999999999998`，出现这种问题到底是为什么？

## 关于作者

一个工作八年的草根程序员。

## 内容

- [JavaScript是如何表示数字的？](#一JavaScript是如何表示数字的)
- [运算时发生了什么？](#二运算时发生了什么)
- 怎么解决精度问题？

### 一、JavaScript是如何表示数字的？

JavaScript使用Number类型表示数字（整数和浮点数），遵循[IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754)标准通过64位来表示一个数字，也就是标准的double双精度浮点数（相关的还有float 32位单精度）。

这样的存储结构优点是可以归一化处理整数和小数，节省存储空间。

64位比特双可以三个部分：

- 符号位S：第1位是正负数符号位(sign)，0代表正数，1代表负数
- 指数位E：中间的11位存储指数(exponent),用来表示次方数
- 尾数位M：最后的52位是尾数(mantissa)，超出的部分自动进一舍零

![](./img/precision.png)

实际数字就可以用以下公式来计算：

![](./img/precision01.png)

对于64位的双精度浮点数，最高的1位是符号位S，接着的11位是指数E，剩下的52位为有效数字M。

 | 位数         | 阶数 | 有效数字/尾数 |
 | ------------ | ---- | ------------- | --- |
 | 单精度浮点数 | 32   | 8             | 23  |
 | 双精度浮点数 | 64   | 11            | 52  |


### 二、运算时发生了什么？

计算机无法直接对十进制的数字进行运算，这是硬件物理特性已经决定的。

- 为什么 0.1+0.2=0.30000000000000004？

#### 2.1 为什么 0.1+0.2=0.30000000000000004？

```
 0.00011001100110011001100110011001100110011001100110011010
+0.00110011001100110011001100110011001100110011001100110100
------------------------------------------------------------
=0.01001100110011001100110011001100110011001100110011001110

```

### 三、怎么解决精度问题？

- 类库
- 解决toFixed
- 浮点数运算精度
- ES6

#### 3.1 类库

NPM上有许多支持JavaScript和Node.js的数学库，比如`Math.js`，`decimal.js`，`D.js`等等。

#### 3.2 解决toFixed

针对toFixed的兼容性问题，我们可以把toFix重写一下来解决，代码如下：

```
Number.prototype.toFixed = function(len){
    if(len>20 || len<0){
        throw new RangeError('toFixed() digits argument must be between 0 and 20');
    }
    // .12转为.0.123
    var number = Number(this);
    if(isNaN(number) || number >= Math.pow(10,21)){
        return number.toString();
    }
    if(typeof(len) == 'unefined' || len === 0){
        return (Math.round(number)).toString();
    }
    var result = number.toString(),
        numberArr = result.split('.');

    if(numberArr.length<2){
        // 需要截取的长度等于当前长度
        return result;
    }

    if(deciNum.length < len){
        //需要截取的长度大于当前长度 1.3.toFixed(2)
        return padNum(result)
    }
    //需要截取的长度小于当前长度，需要判断最后一位数字
    result = intNum + '.' + deciNum.substr(0, len);
    if(parseInt(lastNum, 10)>=5){
        //最后一位数字大于5，要进位
        var times = Math.pow(10, len); //需要放大的倍数
        var changedInt = Number(result.replace('.',''));//截取后转为整数
        changedInt++;//整数进位
        changedInt /= times;//整数转为小数，注：有可能还是整数
        result = padNum(changedInt+'');
    }
    return result;
    //对数字末尾加0
    function padNum(num){
        var dotPos = num.indexOf('.');
        if(dotPos === -1){
            //整数的情况
            num += '.';
            for(var i = 0;i<len;i++){
                num += '0';
            }
            return num;
        } else {
            //小数的情况
            var need = len - (num.length - dotPos - 1);
            for(var j = 0;j<need;j++){
                num += '0';
            }
            return num;
        }
    }
}


```

我们通过判断最后一位是否大于等于5来决定需不需要进位，如果需要进位先把小数乘以倍数变为整数，加1之后，再除以倍数变为小数，这样就不用一位一位的进行判断。

#### 3.3 浮点数运算精度

```
function add(num1,num2){
    const num1Digits = (num1.toString().split('.')[1] || '').length;
    const num2Digits = (num2.toString().split('.')[1] || '').length;
    const baseNum = Math.pow(10,Math.max(num1Digits,num2Digits));
    return (num1 * baseNum + num2 * baseNum) / baseNum;
}

```

#### 3.4 ES6

ES6在Number对象上新增了一个极小的常量——Number.EPSILON，引入一个这么小的量，目的在于为浮点数计算设置一个误差范围，如果误差能够小于Number.EPSILON，我们就可以认为结果是可靠的。

```
function withinErrorMargin(left,right){
    return Math.abs(left-right)< Number.EPSILON
}

withinErrorMargin(0.1+0.2,0.3)
```

### 参考资料

- [0.1 + 0.2不等于0.3？为什么JavaScript有这种“骚”操作？](https://juejin.im/post/5b90e00e6fb9a05cf9080dff)
- [揭秘 0.1 + 0.2 != 0.3](https://www.barretlee.com/blog/2016/09/28/ieee754-operation-in-js/)
- [JavaScript 浮点数陷阱及解法](https://github.com/camsong/blog/issues/9)

## 联系作者

<div align="center">
    <p>
        平凡世界，贵在坚持。
    </p>
    <img src="../img/contact.png" />
</div>