# element-ui升级到2.7.0以上出现的问题

element-ui升级到2.7.0以后需要添加对jsx语法的支持，否则会报一下错误：
```log
error  in ./~/.2.11.1@element-ui/packages/form/src/label-wrap.vue
Syntax Error: Unexpected token (23:14)
  21 |         }
  22 |       }
> 23 |       return (<div class="el-form-item__label-wrap" style={style}>
     |               ^
  24 |         { slots }
  25 |       </div>);
  26 |     } else {
 @ ./~/.2.11.1@element-ui/packages/form/src/label-wrap.vue 4:2-108
```
解决方案：添加对jsx语法的支持
1. 安装jsx依赖
```
cnpm install  babel-plugin-syntax-jsx  babel-plugin-transform-vue-jsx  babel-helper-vue-jsx-merge-props  babel-preset-env  --save-dev
```
2. 在.babelrc中添加对jsx插件的配置
```json
{
  "plugins": ["transform-vue-jsx"]
}

```

