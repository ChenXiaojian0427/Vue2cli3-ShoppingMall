<template>
	<div class="pure-menu pure-menu-horizontal">
	    <ul class="pure-menu-list">
	        <li class="pure-menu-item" v-for="menu in menus"
	        	:key="menu.id" 
	        	:class="[(menu.id == activeId ? 'pure-menu-selected' : ''),'pure-menu-right']"
	        	@click="switchMenu(menu.id)">
	        <router-link :to="menu.path" class="pure-menu-link">{{menu.name}}</router-link>
	        </li>
	        <div class="cart-count" v-if="cartCount">{{cartCount}}</div>
	    </ul>
	</div>
</template>

<script>
	export default {
		name: "Menus",
		data: function() {
			return {
				activeId: 1,
				menus: [{
					id: 1,
					name: "商品",
					path: "/vmall"
					}, {
					id: 2,
					name: "购物车",
					path: "/my-cart"
					},{
					id: 3,
					name: "登录",
					path: "/login"
					}, {
					id: 4,
					name: "注册",
					path: "/register"
					}
				],
				cartCount: 0 //购物车商品数量
			}
		},
		methods: {
			//切换菜单选择状态
			switchMenu: function(menuId) {
				this.activeId = menuId;
			}
		},
		beforeMount: function() { //生命周期函数，监听"购物车"事件
			var that = this;
			that.$product.$on("addToCart", function(productId) {
				that.$product.productIds.push(productId);
				that.cartCount = that.$product.productIds.length;
			});
			that.$product.$on("removeFromCart", function(productId) {
				//删除数组中的指定元素
				for(var i=0;i<that.$product.productIds.length;i++) {
					if(that.$product.productIds[i] == productId) {
						that.$product.productIds.splice(i, 1);
						break;
					}
				}
				that.cartCount = that.$product.productIds.length;
			});
		},
		mounted: function() { //生命周期函数，设置当前选择菜单
			var currentPath = window.location.hash;
			var path = currentPath.split("#")[1];
			if(path.length > 1) { //判断不是根路径
				for(var i=0;i<this.menus.length;i++) {
					if(path == this.menus[i].path) {
						this.activeId = this.menus[i].id;
						break;
					}
				}
			}
		}
	}
</script>

<style scoped>
	.pure-menu-list {position:relative;width:100%}
	.pure-menu-horizontal {padding:0 5%;width:100%; background-color:rgba(0,0,0,.8);}
	.pure-menu-link {margin-right:1px;padding:1em;font-size:14px;color:#eee;}
	.pure-menu-right{float:right;}
	.pure-menu-selected .pure-menu-link, 
	.pure-menu-selected .pure-menu-link:visited {color:#333;background-color:#ffcc33;}
	.pure-menu-active>.pure-menu-link, 
	.pure-menu-link:focus, 
	.pure-menu-link:hover {color:#333;background-color:#ffcc33;}
	.cart-count {position:absolute;top:0;right:0;width:16px;height:16px;
		line-height:16px;text-align:center;font-size:12px;font-weight:bold;
		color:#fff;background-color:#ec0000;border-radius:50%;}
</style>