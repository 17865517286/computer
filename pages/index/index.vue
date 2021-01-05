<template>
	<view class="content">
		<view class="record_sheet">
		</view>
		<view class="record">
			<view class="record" v-if="!upperShow==''">
				{{upperShow}}
			</view>
		</view>

		<view class="expression" v-if="expression=='' && temp==''">
			0
		</view>
		<view class="expression" v-else>
			={{expression}}{{temp}}
		</view>
		<view class="computer_box">
			<view class="expression_button" @click="clear(expression)">AC</view>
			<view class="expression_button" @click="reback(expression,temp)">X</view>
			<view class="expression_button" @click="compute('%')">%</view>
			<view class="expression_button" @click="compute('/')">/</view>
			<view class="num_button" @click="compute(7)">7</view>
			<view class="num_button" @click="compute(8)">8</view>
			<view class="num_button" @click="compute(9)">9</view>
			<view class="expression_button" @click="compute('*')">*</view>
			<view class="num_button" @click="compute(4)">4</view>
			<view class="num_button" @click="compute(5)">5</view>
			<view class="num_button" @click="compute(6)">6</view>
			<view class="expression_button" @click="compute('-')">-</view>
			<view class="num_button" @click="compute(1)">1</view>
			<view class="num_button" @click="compute(2)">2</view>
			<view class="num_button" @click="compute(3)">3</view>
			<view class="expression_button" @click="compute('+')">+</view>
			<view class="num_button">oo</view>
			<view class="num_button" @click="compute(0)">0</view>
			<view class="num_button"  @click="compute('.')">.</view>
			<view class="expression_button" @click="result(expression,temp)">
				<view class="bg_button" >=</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				expression: '',
				temp:'',
				dataType:'',
				middleData: [],
				upperShow:'',
				errorMsg:'出错',
				resultStatus:false,//是否运算
				resultData:'',
				
			}
		},
		onLoad() {
				
		},
		methods: {
			compute(value){
				if(this.temp.length==20 || this.expression.length == 30){
					value = null
				}
				if(this.resultStatus){//判断是否运算后再次运算
				console.log('再次运算开始')
					if(value == '%' || value == '/' || value == '*'|| value == '-' || value == '+'){
						this.resultStatus = false
						this.middleData.push(this.resultData)
						this.expression = this.resultData
						this.temp = value
						this.dataType = (typeof value)//数据类型
					}else{
						this.expression = ''
						this.temp = ''
						this.upperShow = ''
						this.resultStatus = false
					}
					console.log('再次运算结束')
				}
				if(this.temp==''){//输入第一个数值时
					if(value=='%'){
						this.temp = ''
					}else if(value=='.'){
						this.temp = '0.'
					}else{
						this.temp = value //其他值
					}
					this.dataType = (typeof value)//数据类型
				}else{
					if(this.dataType == (typeof value) && (typeof value)== 'number' ){//两次点击都是数字时
						if(this.temp.toString().substr(0,1) == '0'){
							this.temp = '' + value
						}else{
							this.temp = '' + this.temp + value
						}
					}else if( this.dataType != (typeof value) && (typeof value)== 'number'){//第一次是string 第二次点击是数字
						if(this.temp.toString().indexOf('.') != -1){//判断是小数点
								this.temp = '' + this.temp + value
						}else{
							this.expression = '' + this.expression + this.temp 
							this.middleData.push(this.temp)
							this.temp = value
							this.dataType = (typeof value)
						}
					}else if(this.dataType == (typeof value) && (typeof value)== 'string'){//两次都是string
						if(this.temp.toString().indexOf('.') != -1){//判断是否是小数点
							if(value.toString().indexOf('.') != -1){
								this.temp = this.temp
							}else if(value == '%' || value == '/' || value == '*'|| value == '-' || value == '+'){
								this.expression = '' + this.expression + this.temp
								this.middleData.push(this.temp)
								this.temp = value
								this.dataType = (typeof value)
							}
						}else{
							if(this.temp == '%' || this.temp == '/' || this.temp == '*'|| this.temp == '-' || this.temp == '+' ){
								if(value == '%' || value == '/' || value == '*'|| value == '-' || value == '+'){
									this.temp = value
								}
							}else{
								this.temp = this.temp + value
							}
						}
					}else if(this.dataType != (typeof value) && (typeof value)== 'string'){//第一次是数字 第二次点击是string
						if(value.toString().indexOf('.') != -1){
							if(this.temp.toString().indexOf('.') != -1){
								this.temp = this.temp
							}else{
								this.temp = '' + this.temp + value
							 }
						}else{
							this.expression = '' + this.expression + this.temp
							this.middleData.push(this.temp)
							this.temp = value
							this.dataType = (typeof value)
						}
					}
				}
				this.upperShow = '' + this.expression + this.temp
			},
			result(expression,temp){
				console.log('运算开始')
				this.resultStatus = true
				if(this.temp != '' && this.middleData.length >= 1){
					let len = this.middleData[this.middleData.length-1].length-1;
					let charLast = this.middleData[this.middleData.length-1].toString().charAt(len) //获取上一个temp的最后一个字符
					console.log('上一个temp的最后一个字符')
					console.log(charLast)
					console.log('this.temp.toString().charAt(0)')
					console.log(this.temp.toString().charAt(0))
					if((typeof this.temp)== 'string'){
						if(this.temp.toString().indexOf('.') != -1){
							if(this.temp.toString().indexOf('%') != -1  || this.temp.toString().indexOf('/') != -1 || this.temp.toString().indexOf('*') != -1 || this.temp.toString().indexOf('-') != -1 || this.temp.toString().indexOf('+') != -1){
								 this.expression = this.errorMsg
								 this.temp = ''
								 this.upperShow = ''
							}else if(this.temp.toString().charAt(0) == '+' && charLast == '.'){
								this.expression = this.errorMsg
								this.temp = ''
								this.upperShow = ''
							}
						}else if(charLast == '.'){
								this.expression = this.errorMsg
								this.temp = ''
								this.upperShow = ''
						}else{
							this.temp = ''
						}
					}
					if(this.temp != ''){
						this.middleData.push(this.temp)
						this.expression = '' + this.expression + this.temp
						this.temp = ''
					}
				}else{
					if(this.temp != ''){
						this.middleData.push(this.temp)
						this.expression = '' + this.expression + this.temp
						this.temp = ''
					}
				}
				if(this.expression !='出错'){
					this.expression = this.splitArray(this.middleData)
					this.middleData = []
				}
				this.resultData = this.expression
				console.log('运算结束')
			},
			/*分割数组 ：根据 +，- 分割 存放到calcuList里*/
			splitArray(array){
				let tempData = array;
				tempData.push('$')
				let calcuList = [];//存放运算符 + -
				let tempArray = [];//存放临时变量
				let tempArrayResult = [];//存放*，/之间的运算式的中间结果
				let numberList = [];//存放运算符 + -之间的运算式
				for(let j = 0,len=tempData.length; j < len; j++) {
					//判断 +，-
				    if(tempData[j] == '-' || tempData[j] == '+'){
						calcuList.push(tempData[j])
						if(tempArray.length != 0){
							numberList.push(tempArray)
							tempArray = []
						}
					}else if(tempData[j]=='$'){//判断是不是最后一个数
						numberList.push(tempArray)
					}else{
						tempArray.push(tempData[j])
					}
				}
				tempArrayResult = this.calculationOrder(numberList)
				console.log('tempArrayResult赋值')
				console.log(tempArrayResult)
				return this.AdditionSubtraction(tempArrayResult,calcuList)
			},
			getSum(total, num) {
			    return total + num;
			},
			/*加减的运算 ，返回运算的结果 */
			AdditionSubtraction(tempArray,calcuList){
				let sum = 0;//+，-的结果
				if(tempArray.length > 0 && calcuList.length>0){
					let num = 0;//取数的次数
					for(let i=0,len = calcuList.length; i<len; i++){
						num = calcuList.length;
						console.log('num')
						console.log(num)
						if(num>0){
							for(let j = 0; j < num; j++){
								if(j == 0){
									if(calcuList[0] == '+'){
										sum = this.accAdd(tempArray[0],tempArray[1])
									}else{
										sum = this.accSub(tempArray[0],tempArray[1])
									}
								}else{
									if(calcuList[j] == '+'){
										sum = this.accAdd(sum,tempArray[j+1])
									}else{
										sum = this.accSub(sum,tempArray[j+1])
									}
								}
							}
							console.log('sum')
							console.log(sum)
						}	
					}
				}else if(tempArray.length > 0 && calcuList.length==0){
					sum = tempArray.reduce(this.getSum)
				}
				return sum;
			},
			/*乘除的运算 ，返回运算的结果 */
			calculationOrder(tempArray){
				let result = 0;//中间结果
				if(tempArray.length > 0){
					let num = 0;//取数的次数
					for(let x=0,len = tempArray.length; x<len; x++){
						for(let y=0,len2 = tempArray[x].length; y<len2; y++){
							num = (len2-1)/2
						}
						if(num>0){
							for(let i = 1; i <= num; i++){
								if(i == 1){
									if(tempArray[x][1] == '*'){
										result = this.accMul(tempArray[x][0],tempArray[x][2])
									}else if(tempArray[x][1] == '/'){
										result = this.accDiv(tempArray[x][0],tempArray[x][2])
									}else if(tempArray[x][1] == '%'){
										result = tempArray[x][0] % tempArray[x][2]
									}
								}else{
									if(tempArray[x][2*num-1] == '*'){
										result = this.accMul(result,tempArray[x][2*num])
									}else if(tempArray[x][2*num-1] == '/'){
										result = this.accDiv(result,tempArray[x][2*num])
									}else if(tempArray[x][2*num-1] == '%'){
										result = result % tempArray[x][2*num-1]
									}
								}
							}
						}else{
							result = tempArray[x][0]
						}
						tempArray[x] = result
					}
				}
				return tempArray;

			},
			//清除数据
			clear(){
				this.expression = ''
				this.temp = ''
				this.upperShow = ''
				this.resultData = ''
				this.middleData = []
			},
			//回退一个字符
			reback(expression,temp){
				//从数组middleData最后取值给temp，
				this.temp = this.temp.toString()
				if(this.temp == ''){
					if(this.middleData.length>0){
						this.temp = this.middleData.pop();
						this.expression = this.middleData.join('')
						this.temp = this.temp.toString().substr(0,this.temp.length - 1)
					}
				}else{
					this.temp = this.temp.substr(0,this.temp.length - 1)
				}
				this.upperShow = '' + this.expression + this.temp
			},
			//加法
			accAdd(arg1, arg2) {
			    var r1, r2, max;
			    try { r1 = arg1.toString().split(".")[1].length } catch (e) { r1 = 0 }
			    try { r2 = arg2.toString().split(".")[1].length } catch (e) { r2 = 0 }
			    max = Math.pow(10, Math.max(r1, r2))
			    return (arg1 * max + arg2 * max) / max
			},
			//减法
			accSub(arg1, arg2) {
			    var r1, r2, max, min;
			    try { r1 = arg1.toString().split(".")[1].length } catch (e) { r1 = 0 }
			    try { r2 = arg2.toString().split(".")[1].length } catch (e) { r2 = 0 }
			    max = Math.pow(10, Math.max(r1, r2));
			    //动态控制精度长度
			    min = (r1 >= r2) ? r1 : r2;
			    return ((arg1 * max - arg2 * max) / max).toFixed(min)
			},
			//乘法
			accMul(arg1, arg2) {
			    var max = 0, s1 = arg1.toString(), s2 = arg2.toString();
			    try { max += s1.split(".")[1].length } catch (e) { }
			    try { max += s2.split(".")[1].length } catch (e) { }
			    return Number(s1.replace(".", "")) * Number(s2.replace(".", "")) / Math.pow(10, max)
			},
			//除法
			accDiv(arg1, arg2) {
				var t1 = 0, t2 = 0, r1, r2;
				try { t1 = arg1.toString().split(".")[1].length } catch (e) { }
				try { t2 = arg2.toString().split(".")[1].length } catch (e) { }
				r1 = Number(arg1.toString().replace(".", ""))
				r2 = Number(arg2.toString().replace(".", ""))
				return (r1 / r2) * Math.pow(10, t2 - t1)
			}
		}
	}
</script>

<style lang="scss">
@import 'index.scss'
</style>
