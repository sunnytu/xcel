<template>
	<div class="filter_panel" v-show="filterPanelStatus">
		<div class="filter_tag_container">
			<filter-tag-list class="filter_tag_list"></filter-tag-list>
			<button class="submit_btn btn" title="点击筛选并导出文件" 
				@click="filterHandler">
				<svg width="18px" height="15px" viewBox="23 25 18 15" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
				    <!-- Generator: Sketch 40.1 (33804) - http://www.bohemiancoding.com/sketch -->
				    <desc>Created with Sketch.</desc>
				    <defs></defs>
				    <g id="Material/Icons-black/check" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd" transform="translate(23.000000, 25.000000)">
				        <polygon id="Shape" fill="#FFFFFF" points="5.9999939 11.2 1.7999939 7 0.399993896 8.4 5.9999939 14 17.9999939 2 16.5999939 0.6"></polygon>
				    </g>
				</svg>
			</button>
		</div>
		<div class="filter_form_group">
			<div class="filter_form_group_inside">
				<filter-form-single-logic></filter-form-single-logic>
				<filter-form-multi-calc></filter-form-multi-calc>
				<filter-form-double-cols-range></filter-form-double-cols-range>
			</div>
		</div>
	</div>
</template>

<script>
	
	import FilterTagList from './FilterTagList'
	import FilterFormSingleLogic from './FilterFormSingleLogic'
	import FilterFormMultiCalc from './FilterFormMultiCalc'
	import FilterFormDoubleColsRange from './FilterFormDoubleColsRange'
	import { getFilterTagList, getFilterPanelStatus, getCurFilterTagListCount, getFilterWay, getFileStatus, getActiveSheet } from '../../vuex/getters'
	import { setFilteredData, setFileStatus } from '../../vuex/actions'
	import { ipcRenderer } from 'electron'
	export default{
		components: {
			FilterTagList,
			FilterFormSingleLogic,
			FilterFormMultiCalc,
			FilterFormDoubleColsRange
		},
		data() {
			return {
				curCol: 1,
				filterVal: '',
				colOfSheet: 1,
				filterFormNav: ['单列（组合）逻辑', '多列运算逻辑', '双列范围逻辑'],
				activeFilterFormIndex: 0
			}
		},
		vuex: {
			getters: {
				filterPanelStatus: getFilterPanelStatus,
				filterTagList: getFilterTagList,
				filterWay: getFilterWay,
				fileStatus: getFileStatus,
				curFilterTagListCount: getCurFilterTagListCount,
				activeSheet: getActiveSheet
			},
			actions: {
				setFilteredData,
				setFileStatus
			}
		},
		created(){
			ipcRenderer.on('filter-response', (event, arg) => {
				this.setFileStatus(2)
      	this.setFilteredData(arg.filRow)
      	ipcRenderer.send('exportFile-start')
	    })

	    ipcRenderer.on('exportFile-response', (event, arg) => {
	    	console.log(arg.info)
	    	this.setFileStatus(-1)
	    	
	    	if(arg.type === -1) {
	    		setTimeout(() => {
	    			ipcRenderer.send('sync-alert-dialog', {
		    			content: arg.info
		    		})
	    		}, 30)
	    	}
	    })
		},
		methods: {
			submit(){
				if(filterVal.tirm().length === 0) return false
			},
			filterHandler(){
				if(this.curFilterTagListCount === 0) {
					ipcRenderer.send('sync-alert-dialog', {
						content: '请先添加筛选条件'
					})
				}else {
					this.setFileStatus(1) 
					ipcRenderer.send('filter-start', {
			      filterTagList: this.filterTagList,
			      filterWay: this.filterWay,
			      curActiveSheetName: this.activeSheet.name
			    })
				}
			}
		}
	}
</script>

<style lang="scss">
	.filter_panel{
		position: fixed;
		bottom: 56px;
		left: 0;
		right: 0;
		z-index: 10;
	}

	.filter_tag_container{
		min-height: 64px;
		background-color: #fff;
		box-shadow: 0 -2px 4px rgba(0, 0, 0, .2);
		padding: 20px 27px 15px;
		position: relative;
	}

	.filter_tag_list {
		margin-right: 64px;
		&+button {
			width: 64px;
			height: 64px;
			position: absolute;
			right: 0;
			bottom: 0;
			background-color: #4285F4;
			color: #fff;
			border: 0;
			outline: 0;
			cursor: pointer;
		}
	}
	.filter_form_group{
		background-color: #2B3244;
		padding: 22px 20px;
		overflow: auto;
		&_inside {
			min-width: 1080px;
			background-color: #fff;
		}
		.table {
			margin-bottom: 0;
			width: 100%;
		}
		form {
			position: relative;
			&:not(:last-child)::after {
				content: "\20";
				position: absolute;
				bottom: 0px;
				width: 100%;
				border-bottom: 1px solid rgba(0, 0, 0, .1)
			}
		}
	}

	.filter_form_group table tr td{
	  color: #000;
	  font-size: 12px;
	  height: 54px;
		color: #2B3244;

	  .val_mask {
	  	color: #2B3244;
	  }
	  &:first-child{
	    width: 130px;
	    text-align: center;
	  }
	  &:nth-child(2) {
	    width: 82px;
	  }
	  &:nth-child(3) {
	    width: 127px;
	    p {
	    	width: 107px;
	      line-height: 24px;
				border: 1px solid #D8D8D8;
				text-align: center;
				cursor: pointer;
	    }
	  }
	  &:nth-child(4) {
	    width: 127px;
	    .select {
	      width: 107px;
	    }
	  }
	  &:nth-child(5) {
	    width: 127px;
	    .select {
	      width: 107px;
	    }
	  }
	  &:nth-child(6) {
	    width: 317px;
	    input{
	      width: 300px;
	      line-height: 24px;
	    }
	  }
	  &:last-child {
	    button{
	      background-color: #4285F4;
	      font-size: 12px;
	      text-align: center;
	      line-height: 24px;
	      border-radius: 13px;
	      color: #fff;
	      width: 66px;
	      padding: 0;
	      border: 0;
	      outline: 0;
	      cursor: pointer;
	    }
	  }
	}

	.table tr td{
	  vertical-align: middle;
	}

	input[type="text"]{
	  font-size: 12px;
	  text-align: center;
	  outline: 0;
	  border:1px solid #D8D8D8;
	  outline: none;
	  padding: 0;
	  &:disabled {
			cursor: not-allowed;
			background-color: #eee;
	  }
	}
	
</style>

