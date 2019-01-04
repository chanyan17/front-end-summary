## vue中通过路由跳转的三种方式

1. router-link

    ```html

    <router-link to="/user">点击验证动画效果 </router-link>

    ```

    * 要跳转的路径在需要提前在`router/index.js`下引入

2. this.$router.push

    ```javascript

    this.$router.push({path: '/user'})

    this.$router.push({path: '/user', query: { id: 123 }, params: {name: 'LILI'}})

    this.$router.push({name: 'user', params: {id: 123}, query: {name: 'LILI'}})

    // this.$route.query.id,  // 获取query参数
    // this.$route.params.id  // 获取params参数
    // query类似get，在浏览器地址栏中显示参数 /user/LILI?id=123
    // parmas 路由后面添加 /user/123/?name=LILI

    ```

    * 使用params传参需要在路由定义的时候添加参数名{path: '/user/:id', name: 'user', ...}

3. this.$router.replace

    ```javascript

    this.$router.replace{path：'/' }

    ```
