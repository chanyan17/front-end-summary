## vue表格中图片的显示

### 单张图片
```html

<el-table-column
    label="头像"
    width="60">
    <template slot-scope="scope">
    <img :src="scope.row.portrait" width="30" height="30"/>
    </template>
</el-table-column>

```

* `scope` 属性在2.5以后的版本中已经废弃， 被 `slot-scope` 替代

### 多张图片

```html

<el-table-column prop="pictures" label="描述图片">
　　<template slot-scope="scope">
　　　　<img v-for="(item, index) in scope.row.pictures" :src="item" width="40" height="40" class="head_pic" v-bind:key="index"/>
　　</template>
</el-table-column>

```