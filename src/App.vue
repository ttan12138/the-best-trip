<template>
  <div id="app">
    <div id="header">
		旅客行程智能推荐系统
		<!-- <div id="allmap"></div> -->
	</div>
	<div id="content">
		<div class="in-content" id="in-start">
			<span class="L0">出发地：</span>
			<span>
			<!-- <el-input v-model="address_detail" id="suggestId"></el-input>
			-->
				<el-input @focus="getLocationInfo" v-model="address_detail" placeholder="请选择出发地"></el-input>
				<el-dialog title="地理位置(关键词可快速搜索地址,滑轮放大缩小,按esc退出)" :visible.sync="getLocationInfoDialog" width="1200px" @opened="mapShow">
					<div class="mapContent">
						<el-select v-model="state" filterable remote reserve-keyword placeholder="请输入关键词" @change="handleSelect" :remote-method="querySearchAsync" :loading="locationStelectLoading" style="height:20px;width: 400px;position: relative;top: -30px;">
							<el-option class="reaultshow" v-for="item in pois" :label="item.label" :key="item.value" :value="item.value"></el-option>
						</el-select>
						<div id="allMap" style="width: 100%; height: 500px;position: relative;top: -10px;"></div>
					</div>
				</el-dialog>
			</span>
		</div>
		<div class="in-content" id="in-over">
			<span class="L0">目的地：</span>
			<span class="L1">
				<el-select v-model="input2" clearable placeholder="请选择目的地">
					<el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value"></el-option>
				</el-select>
				</span>
		</div>
		<div class="in-content" id="in-time">
			<span class="L0">出发时间：</span>
			<span>
				<el-date-picker v-model="value1" type="datetime" value-format="timestamp" placeholder="选择日期时间"></el-date-picker>
			</span>
		</div>
		<button type="submit"  style="color: #FFFFFF;" v-on:click="onsubmit">查询</button>
	</div>
	<div id="result" v-on:click="onsubmit">
		<el-tabs v-model="activeName" type="border-card" style="width: 50%;">
			<el-tab-pane label="价钱" name="price">
				<ul style="height: 500px;">
					<li v-for="(item, index) in prince" :key="index">{{ item }}</li>
				</ul></el-tab-pane>
			<el-tab-pane label="时间" name="time" v-on:click="onsubmit">
				<ul style="height: 500px;">
					<li v-for="(item, index) in out_time" :key="index">{{ item }}</li>
				</ul></el-tab-pane>
			<el-tab-pane label="高铁/火车" name="transMode" v-on:click="onsubmit">
				<el-tabs v-model="huo_gao" :tab-position="tabPosition" style="height: 500px;">
					<el-tab-pane label="火车" name="train">
						<ul style="height: 500px;">
							<li v-for="(item, index) in trip_type1" :key="index">{{ item }}</li>
						</ul></el-tab-pane>
					<el-tab-pane label="高铁" name="highSpeedTrains">
						<ul style="height: 500px;">
							<li v-for="(item, index) in trip_type2" :key="index">{{ item }}</li>
						</ul></el-tab-pane></el-tabs>	
			</el-tab-pane>
		</el-tabs>	
	</div>
	<div id="l-map"></div>
	<div id="r-result"></div>
  </div>
</template>
<script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&ak=zDtWPHKbbKPueoWo9WYMsKzso84wiL7X"></script>
<!-- <script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&ak=zDtWPHKbbKPueoWo9WYMsKzso84wiL7X&callback=init"></script> -->

<script type="text/javascript" src="http://api.map.baidu.com/library/Heatmap/2.0/src/Heatmap_min.js"></script>
<script>
	import axios from 'axios'
	import VueAxios from 'vue-axios'
export default {
  name: 'App',
  data() {
        return {
			input1:'',
			input2:'',
			value1: '',
			activeName: 'price',
			huo_gao:'train',
			prefer:'',
			prince:['价钱', '价钱'],
			out_time:['时间', '时间'],
			trip_type1:['火车', '高铁'],
			trip_type2:['高铁', '火车'],
			options: [{
					value: 'kunming',
						label: '昆明'
					}, {
					value: 'shijiazhuang',
						label: '石家庄'
					}, {
					value: 'shanghai',
						label: '上海'
					}],
			tabPosition: 'left',
			companyInfo: {// 输入框没有数据默认的地理位置
				company_location: "116.404, 39.915", 
			},
			Location: '',
			latitude:'',
			lngitude:'',
			getLocationInfoDialog: false,
			state: '',
			locationStelectLoading: false,
			pois:'',
			/* userlocation:{
				lng:"",
				lat:""
			},*/
			address_detail:'',
			mapMarkIcon:('放一个标注的图标地址')
		}
	},
  components: {
	},
  methods: {
		onsubmit(){
			/* console.log(this.latitude + " " +this.lngitude);
			console.log(this.value1);
			console.log(this.input2);
			console.log(this.$axios); */
			if(this.value1 && this.input2 && this.latitude){
				if(this.activeName == 'transMode'){
					this.prefer = this.huo_gao;
				}else{
					this.prefer = this.activeName;
				}
				/* console.log(this.prefer); */
				var getS = 'http://47.94.19.235:9001/trip?latitude='+this.latitude+"&lngitude="+this.lngitude+"&city="+this.input2+"&prefer="+this.prefer+"&time="+this.value1;
				/* var getS = 'http://baidu.com/trip?latitude='+this.latitude+"&lngitude="+this.lngitude+"&city="+this.input2+"&prefer="+this.prefer+"&time="+this.value1; */
				console.log(getS);
				this.$axios.get(getS)
				.then(res=>{
					console.log(res);
				})
				.catch(err=>{
					console.log(err);
				});
			}else if(!this.latitude){
				alert('请选择出发地信息');
			}else if(!this.input2){
				alert('请选择目的地地信息');
			}else if(!this.value1){
				alert('请选择出发时间');
			}else{
				alert("请联系管理员");
			}
			
		},
		handleClick(tab, event){
			console.log(tab, event);
		},
		getLocationInfo() {
			this.getLocationInfoDialog = true;
		},
		mapShow() {//弹窗打开时触发
			// console.log("创建地图")
			this.map = new BMap.Map("allMap");
			// let myIcon = new BMap.Icon(this.mapMarkIcon, new BMap.Size(30, 40));
			if (this.Location) {
				let point = new BMap.Point(this.Location.split(',')[0], this.Location.split(',')[1])
				this.map.centerAndZoom(point, 10)
				// let marker = new BMap.Marker(point, { icon: myIcon })
				let marker = new BMap.Marker(point)
				this.map.addOverlay(marker);
		
			} else if (this.companyInfo.company_location) {
				let lng = this.companyInfo.company_location.split(',')[0];
				let lat = this.companyInfo.company_location.split(',')[1];
				let point = new BMap.Point(lng, lat);
				this.map.centerAndZoom(point, 10)
			}
			this.map.addEventListener("click", (ev) => {
				this.map.clearOverlays();
				let point = new BMap.Point(ev.point.lng, ev.point.lat);
				// var newMarker = new BMap.Marker(point, { icon: myIcon });
				var newMarker = new BMap.Marker(point);
				this.map.centerAndZoom(point,17);
				this.map.addOverlay(newMarker);    //添加标注
				this.Location = point.lng + ',' + point.lat;
				this.latitude = point.lat;
				this.lngitude = point.lng;
				var gc = new BMap.Geocoder();
				let _this = this;
					gc.getLocation(point, function (rs) {
						var addComp = rs.addressComponents;
						_this.address_detail = rs.address;
						/* alert(rs.addressComponents);//地址信息
						alert(rs.address) */
						//_this.locData.address = rs.address;
				});
				/* var pt = ev.point;
				var addComp = this.map.addressComponents;
				var geoc = new BMap.Geocoder(); 
				geoc.getLocation(pt, function(rs){
				this.Address = addComp.province + addComp.city + addComp.district + addComp.street + addComp.streetNumber;
				}); */
			});
			this.map.disableDoubleClickZoom(true); //双击放大
			this.map.enableScrollWheelZoom(); //启用滚轮放大缩小，默认禁用
			this.map.enableAutoResize(); //启用自动适应容器尺寸变化，默认启用
			var _map = this.map;
			this.local = new BMap.LocalSearch(this.map, { //智能搜索
				onSearchComplete: (results) => {
					let pois = [];
					for (var i = 0; i < results.getCurrentNumPois(); i++) {
						let poi = results.getPoi(i)
						var point = results.getPoi(i).point
						pois.push({ label: poi.title + "(" + poi.address + ")", value: point.lng + ',' + point.lat });
						/* document.getElementsByClassName('reaultshow')[i].onclick = function(){
									if(point.lng != "" && point.lat != ""){
										_map.clearOverlays(); 
										var new_point = new BMap.Point(point.lng,point.lat);
										var marker = new BMap.Marker(new_point);  // 创建标注
										_map.addOverlay(marker);              // 将标注添加到地图中
										_map.panTo(new_point);      
									}
						} */
					}
					
					this.pois = pois;
					this.locationStelectLoading = false;
				},
			});
		},
		handleSelect(station_loc) {
			this.map.clearOverlays();
			/* let myIcon = new BMap.Icon("../img/marker_icon.png", new BMap.Size(30, 40)); */
			let point = new BMap.Point(station_loc.split(',')[0], station_loc.split(',')[1])
			/* marker = new BMap.Marker(point, { icon: myIcon }) */
			let marker = new BMap.Marker(point)
			this.map.centerAndZoom(point,17);
			this.map.addOverlay(marker);
			this.Location = point.lng + ',' + point.lat;
			this.latitude = point.lat;
			this.lngitude = point.lng;
			var gc = new BMap.Geocoder();
			let _this = this;
				gc.getLocation(point, function (rs) {
					var addComp = rs.addressComponents;
					_this.address_detail = rs.address;
					/* alert(rs.addressComponents);//地址信息
					alert(rs.address) */
					//_this.locData.address = rs.address;
			});
			
		},
		querySearchAsync(queryString) {
			this.locationStelectLoading = true;
			this.local.search(queryString);
		},
		
	} ,
	mounted() {
		/* this.$nextTick(function(){
			MP("zDtWPHKbbKPueoWo9WYMsKzso84wiL7X").then( BMap => {
			var th = this;
			var map = new BMap.Map("allmap");
			var point = new BMap.Point(121.160724,31.173277);
			map.centerAndZoom(point,15);
			map.enableScrollWheelZoom();
			var ac = new BMap.Autocomplete(
			{
				"input":"suggestId",
				"location":map
			});
			var myValue;
			ac.addEventListener("onconfirm",function(e){
				var _value = e.item.value;
				myValue = _value.province + _value.city + _value.district + _value.street + _value.business;
				this.address_detail = myValue
				setPlace();
			});
				
			function setPlace() {
				map.clearOverlays();//清除地图上所有覆盖物
				function myFun() {
					th.userlocation = local.getResults().getPoi(0).point;    //获取第一个智能搜索的结果
					map.centerAndZoom(th.userlocation, 18);
					map.addOverlay(new BMap.Marker(th.userlocation));    //添加标注
				}
				
			var local = new BMap.LocalSearch(map, { //智能搜索
				onSearchComplete: myFun
			});
			local.search(myValue);
			//测试输出坐标（指的是输入框最后确定地点的经纬度）
			map.addEventListener("click",function(e){
			//经度
				console.log(th.userlocation.lng);
			//维度
				console.log(th.userlocation.lat);
			});
			}
			})
		}); */
	}
}
</script>

<style scoped>
*{
	margin: 0 auto;
}
.mapContent{
	position: relative;
}
#l-map{height:500px;width:100%;}
#r-result{width:100%;}
#app {
  width: 100%;
  height: 1000px;
  color: #FFFFFF;
  left: center;
  top: center;
  text-align: center;
  background: url(../../基础项目/003图片使用/img/2.jpg) no-repeat;
  
}
#content {
	position: absolute;
	margin: auto 0;
	width: 100%;
	height: 320px;
	color: #FFFFFF;
	background-color: rgba(255, 255, 255, 0.3);
	
}
.in-content{
	margin: 0 auto;
    top: 10px;
	position: relative;
	line-height: 30px;
	text-align:center;
}
#content .in-content span{
	top: -50px;
	left: -30px;
	width: 220px;
	height: 30px;
	display: block;
}

#content .in-content .L0 {
	top: 35px;
	left: -150px;
	position: relative;
	display: inline;
}
#content .in-content .L0 .L1{
	top: 40px;
	left:150px;
	position: relative;
	display: inline;
}
/*
.in-content input {
	border-radius: 4px;
}
#in-time #in-start #in-over {
	top: 10px;
	position: relative;
}*/

#content button {
	top: 35px;
	position: relative;
	margin: 10px;
	width: 150px;
	height: 30px;
	background-color: #1AA034;
	border-radius: 7px;
} 
#result {
	position: relative;
	top:330px;
	color: #000000;
	text-align: left;
}
</style>

