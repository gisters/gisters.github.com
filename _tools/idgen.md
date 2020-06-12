---
title: "身份证号码"
layout: page
---

<form name="form1">
    <input type="hidden" value="select" name="action">
    <div>
        <span>地点：</span>
        <select name="p" id="p"></select>
        <select name="c" id="c"></select>
        <select name="r" id="r"></select>
        <span>&nbsp;&nbsp;时间：</span>
        <select onchange="YYYYDD(this.value,document.form1.SMonth,document.form1.SDay)" id="y"   name="SYear"><option value="2016">2016</option></select>年<select onchange="MMDD(this.value,document.form1.SYear,document.form1.SDay)" id="m" name="SMonth"><option value="1">1</option></select>月<select id="d" name="SDay"><option value="1">1</option></select>日
    </div>
    <div>
        <span>性别：</span><label><input type="radio" name="g" id="g1" value="1" checked="checked">男</label>
        <label><input type="radio" name="g" id="g2" value="2">女</label>
        <span>&nbsp;&nbsp;提交：</span><input type="text" name="n" id="n" size="3" maxlength="3" style="width:50px;" value="5">个
    </div>
    <div>
        <input id="go" value="提交" type="button" class="btn btn-primary" onclick="ock()">
    </div>
</form>
<div class="rNo" id="rNo"></div>

<script language="javascript" src="/cdn/js/id-gen.js"></script>
<script language="javascript">
    window.onload = function(){
        YYYYMMDDstart(document.form1,document.form1.SYear,document.form1.SMonth,document.form1.SDay),YYYYMMDDstart(document.form1,document.form1.EYear,document.form1.EMonth,document.form1.EDay)
    }
    new PCAS("p","c","r","110000-北京市","110100-市辖区","110101-东城区");
</script>
