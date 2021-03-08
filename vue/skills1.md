## vue实战技巧

1. vue组件内部监听生命周期函数，可以用 `$on , $once` 去监听所有的生命周期钩子函数，在一些组件如果需要注销绑定的函数时特别有用

   ```vue
   export default{
   	mounted(){
   		this.chart = echarts.init(this.$el);
   
   		window.addEventListener('resize', this.$_handleResizeChart);
   		// 通过hook监听组件注销钩子函数，并取消监听函数
   		this.$once('hook:beforeDestroy',()=> {
   			window.removeEventListener('resize', this.$_handleResizeChart)
   		});
   
   		// 如果是监听组件的 updated 钩子函数
   		this.$on('hook:updated', ()=>{})
   	}
   }
   ```

2. 开发中引用第三方组件，需要监听第三方组件数据的变化，可以考虑在外部监听组件的`updated`钩子函数，vue支持在外部监听组件的生命周期函数

   ```javascript
   <template>
     <custom-select @hook:updated="handleSelectUpdated"
   </template>
   <script>
       import CustomSelect from 'XXX'
   		export default{
         components:{CustomSelect},
         methods:{
           handleSelectUpdated(){
             console.log('子组件的updated钩子函数被触发时，该函数执行')
           }
         }
       }
   </script>
   ```

   