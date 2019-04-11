<template>
  <el-container style="border: 1px solid #eee">
    <el-row style="width: 100%">
      <el-col :xs="10" :sm="10" :md="8" :lg="6" :xl="4" class="aside">
        <div class="asideButton">
          <el-row>
            <el-col>
              <el-button type="primary" style="width: 220px" size="medium" plain round @click="handleNew">发布新地址</el-button>
            </el-col>
          </el-row>
          <el-row>
            <el-col>
              <el-input
                placeholder="搜索分类"
                prefix-icon="el-icon-search"
                v-model="filterText"
                size="medium">
              </el-input>
            </el-col>
          </el-row>
        </div>
        <el-aside style="padding-top:10px">
          <el-tree
            :data="setTree"
            :props="defaultProps"
            node-key="id"
            ref="SlotMenuList"
            :filter-node-method="filterNode"
            @node-contextmenu='rihgtClick'
            accordion
            >
            <span class="slot-t-node" slot-scope="{ node, data }">
              <span v-show="!node.isEdit">
                <span v-show="data.children && data.children.length >= 1">
                  <i :class="{ 'fa fa-plus-square': !node.expanded, 'fa fa-minus-square':node.expanded}" />
                  <span :class="[data.id >= maxexpandId ? 'slot-t-node--label' : '']">{{node.label}}</span>
                </span>
                <span v-show="!data.children || data.children.length == 0">
                  <i class='' style='margin-right:10px'></i>
                  <span :class="[data.id >= maxexpandId ? 'slot-t-node--label' : '']">{{node.label}}</span>
                </span>
              </span>
            <!-- 编辑输入框 -->
              <span v-show="node.isEdit">
                <el-input class="slot-t-input" size="mini" autofocus
                  v-model="data.name"
                  :ref="'slotTreeInput'+data.id"
                  @blur.stop="NodeBlur(node, data)"
                  @keyup.enter.native="NodeBlur(node, data)"></el-input>
              </span>
            </span>
          </el-tree>
          <!--鼠标右键点击出现页面-->
          <div v-show="menuVisible">
            <el-menu
              id = "rightClickMenu"
              class="el-menu-vertical"
              @select="handleRightSelect"
              active-text-color="#fff"
              text-color="#fff">
              <el-menu-item index="1" class="menuItem">
                <span slot="title">添加分类</span>
              </el-menu-item>
              <el-menu-item index="2" class="menuItem">
                <span slot="title">修改分类</span>
              </el-menu-item>
              <el-menu-item index="3" class="menuItem">
                <span slot="title">删除分类</span>
              </el-menu-item>
              <hr style="color: #ffffff">
              <el-menu-item index="4" class="menuItem">
                <span slot="title">添加标签</span>
              </el-menu-item>
            </el-menu>
          </div>
        </el-aside>
      </el-col>
      <el-col :xs="14" :sm="14" :md="16" :lg="18" :xl="20">
        <el-container>
          <el-main>
            <el-row>
              <el-col :md="8" :lg="10" :xl="12">
                <label>当前分类：</label>
                <label>{{input2}}</label>
              </el-col>
              <el-col :md="16" :lg="14" :xl="12">
                <el-row>
                  <el-col :md="6" :lg="9" :xl="12">
                    <el-row>
                      <el-col :md="10" :lg="8" :xl="4">
                        <label>标签名称</label>
                      </el-col>
                      <el-col :md="14" :lg="16" :xl="20">
                        <el-input v-model="input" placeholder="输入标签名称"></el-input>
                      </el-col>
                    </el-row>
                  </el-col>
                  <el-col :md="5" :lg="4" :xl="3" :offset="3">
                    <el-button type="primary" plain>搜索</el-button>
                  </el-col>
                  <el-col :md="5" :lg="4" :xl="3">
                    <el-button plain>重置</el-button>
                  </el-col>
                  <el-col :md="5" :lg="4" :xl="3">
                    <el-button type="primary" @click="handleNew">新增</el-button>
                  </el-col>
                </el-row>
              </el-col>
            </el-row>
            <el-table :data="tableData" :stripe="true" class="el-table" :header-cell-style="{background:'#FAFAFA'}">
              <el-table-column prop="tagID" label="编号">
              </el-table-column>
              <el-table-column prop="name" label="名称">
              </el-table-column>
              <el-table-column prop="description" label="描述">
              </el-table-column>
              <el-table-column prop="creatorID" label="创建人">
              </el-table-column>
              <el-table-column prop="regeneratorID" label="更新人">
              </el-table-column>
              <el-table-column label="操作" width="120px">
                <template slot-scope="scope">
                  <el-tooltip class="item" effect="dark" content="属性修改" placement="bottom">
                    <i class="el-icon-edit" style="cursor: pointer;"  @click="handleEdit(scope.$index, scope.row)"></i>
                  </el-tooltip>
                  <el-tooltip class="item" effect="dark" content="分类修改" placement="bottom">
                    <i class="fa fa-th-large" style="cursor: pointer;" aria-hidden="true" @click="handleClassifyEdit(scope.$index, scope.row)"></i>
                  </el-tooltip>
                </template>
              </el-table-column>
            </el-table>
            <el-row>
              <el-col :md="3" :lg="6" :xl="7">
                <el-input type="hidden"></el-input>
              </el-col>
              <el-col :md="18" :lg="12" :xl="10">
                <div class="block">
                  <!--<span class="demonstration">完整功能</span>-->
                  <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage4"
                    :page-sizes="[10, 20, 30, 40]"
                    :page-size="10"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="40">
                  </el-pagination>
                </div>
              </el-col>
              <el-col :md="3" :lg="6" :xl="7">
                <el-input type="hidden"></el-input>
              </el-col>
            </el-row>
          </el-main>
        </el-container>
      </el-col>
    </el-row>
        <!-- modal edit -->
    <el-dialog title="标签属性修改" :visible.sync="dialogFormVisible" width='30%'>
      <el-form :model="editObj" label-width="80px">
        <el-form-item label="标签名称">
          <el-input
            type="textarea"
            :rows="2"
            autocomplete="off"
            placeholder="请输入内容"
            v-model="editObj.name">
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
      </div>
    </el-dialog>
    <!-- modal Classify edit -->
    <el-dialog title="标签分类修改" :visible.sync="dialogClassifyVisible" width='30%'>
      <el-tree
        :data="setTree"
        :props="defaultProps"
        node-key="id"
        ref="SlotMenuList"
        @node-contextmenu='rihgtClick2'
        accordion
        >
        <span class="slot-t-node" slot-scope="{ node, data }">
          <span v-show="!node.isEdit">
            <span v-show="data.children && data.children.length >= 1">
              <i :class="{ 'fa fa-plus-square': !node.expanded, 'fa fa-minus-square':node.expanded}" />
              <span :class="[data.id >= maxexpandId ? 'slot-t-node--label' : '']">{{node.label}}</span>
            </span>
            <span v-show="!data.children || data.children.length == 0">
              <i class='' style='margin-right:10px'></i>
              <span :class="[data.id >= maxexpandId ? 'slot-t-node--label' : '']">{{node.label}}</span>
            </span>
          </span>
        <!-- 编辑输入框 -->
          <span v-show="node.isEdit">
            <el-input class="slot-t-input" size="mini" autofocus
              v-model="data.name"
              :ref="'slotTreeInput'+data.id"
              @blur.stop="NodeBlur(node, data)"
              @keyup.enter.native="NodeBlur(node, data)"></el-input>
          </span>
        </span>
      </el-tree>
      <!--鼠标右键点击出现页面-->
      <div v-show="menuVisible2">
        <el-menu
          id = "rightClickMenu2"
          class="el-menu-vertical"
          @select="handleRightSelect1"
          active-text-color="#fff"
          text-color="#fff">
          <el-menu-item index="1" class="menuItem">
            <span slot="title">添加分类</span>
          </el-menu-item>
          <el-menu-item index="2" class="menuItem">
            <span slot="title">修改分类</span>
          </el-menu-item>
          <el-menu-item index="3" class="menuItem">
            <span slot="title">删除分类</span>
          </el-menu-item>
          <hr style="color: #ffffff">
          <el-menu-item index="4" class="menuItem">
            <span slot="title">添加标签</span>
          </el-menu-item>
        </el-menu>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogClassifyVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogClassifyVisible = false">确 定</el-button>
      </div>
    </el-dialog>
      <!-- modal new edit -->
    <el-dialog title="创建标签" :visible.sync="dialogNewFormVisible" width='30%'>
      <el-form label-width="80px">
        <el-form-item label="标签名称">
          <el-input autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="标签描述">
          <el-input
            type="textarea"
            :rows="3"
            autocomplete="off"
            placeholder="请输入内容"
            >
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogNewFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogNewFormConfirm">确 定</el-button>
      </div>
    </el-dialog>
  </el-container>
</template>
<script>
import ElCol from "element-ui/packages/col/src/col";
import api from '@/resource/api'
api.treelist = api.treelist.splice(0,10)
let id = 1000;
export default {
  // watch: {
  //   filterText(val) {
  //     this.$refs.SlotMenuList.filter(val);
  //   }
  // },
  components: { ElCol },
  data() {
    const item = {
      tagID: "ID001",
      name: "地区",
      description: "此处是改内容的详细描述...",
      creatorID: "Admin",
      regeneratorID: "Admin"
    };
    return {
      DATA: null,
      NODE: null,
      dialogNewFormVisible: false,
      dialogFormVisible: false,
      dialogClassifyVisible: false,
      tableData: Array(10).fill(item),
      maxexpandId: api.maxexpandId,//新增节点开始id
			non_maxexpandId: api.maxexpandId,//新增节点开始id(不更改)
			isLoadingTree: true,//是否加载节点树
			setTree: api.treelist,//节点树数据
			defaultProps: {
				children: 'children',
				label: 'name'
			},
      filterText: '',
      input: "",
      input2: "",
      currentPage4: 4,
      editObj: {},
      menuVisible: false,
      objectID: null,
      /*分类修改*/
      menuVisible2: false,
      objectID2: null
    };
  },
  methods: {
    filterNode(value, data) {
      console.log('value:',value)
      console.log('data:',data)
      if (!value) return true;
      return data.label.indexOf(value) !== -1;
    },
    handleRightSelect(key) {
      console.log(key);
      if (key == 1) {
        this.NodeAdd(this.NODE, this.DATA);
        this.menuVisible = false;
      } else if (key == 2) {
        this.NodeEdit(this.NODE, this.DATA);
        this.menuVisible = false;
      } else if (key == 3) {
        this.NodeDel(this.NODE, this.DATA);
        this.menuVisible = false;
      } else if(key == 4){
        console.log('4')
        this.menuVisible = false;
      }
    },
    handleRightSelect1(key) {
      console.log(key);
      if (key == 1) {
        this.NodeAdd(this.NODE, this.DATA);
        this.menuVisible2 = false;
      } else if (key == 2) {
        this.NodeEdit(this.NODE, this.DATA);
        this.menuVisible2 = false;
      } else if (key == 3) {
        this.NodeDel(this.NODE, this.DATA);
        this.menuVisible2 = false;
      } else if(key == 4){
        console.log('4')
      }
    },
		// handleAddTop(){//添加顶级节点
		// 	this.setTree.push({
		// 		id: ++this.maxexpandId,
		// 		name: '新增顶级节点',
		// 		pid: '',
		// 		children: []
		// 	})
		// },
		NodeBlur(n, d){//输入框失焦
			console.log(n, d)
			if(n.isEdit){
				this.$set(n, 'isEdit', false)
			}
			this.$nextTick(() => {
				this.$refs['slotTreeInput'+d.id].$refs.input.focus()
			})
		},
		NodeEdit(n, d){//编辑节点
			console.log(n, d)
			if(!n.isEdit){//检测isEdit是否存在or是否为false
				this.$set(n, 'isEdit', true)
			}
		},
		NodeDel(n, d){//删除节点
			console.log(n, d)
			let that = this;
			if(d.children && d.children.length !== 0){
				this.$message.error("此节点有子级，不可删除！")
				return false;
			}else{
				//新增节点可直接删除，已存在的节点要二次确认
				//删除操作
				let DelFun = () => {
					let _list = n.parent.data.children || n.parent.data;//节点同级数据
					let _index = _list.map((c) => c.id).indexOf(d.id);
					console.log(_index)
					_list.splice(_index, 1);
					this.$message.success("删除成功！")
				}
				//二次确认
				let ConfirmFun = () => {
					this.$confirm("是否删除此节点？","提示",{
						confirmButtonText: "确认",
						cancelButtonText: "取消",
						type: "warning"
					}).then(() => {
						DelFun()
					}).catch(() => {})
				}
				//判断是否是新增节点
				d.id > this.non_maxexpandId ? DelFun() : ConfirmFun()
			}
		},
		NodeAdd(n, d){//新增节点
			console.log(n, d)
			//判断层级
			if(n.level >= 3){
				this.$message.error("最多只支持三级！")
				return false;
			}
			//新增数据
			d.children.push({
				id: ++this.maxexpandId,
				name: '新增节点',
				pid: d.id,
				children: []
			})
			//同时展开节点
			if(!n.expanded){
				n.expanded = true
			}
		},
    rihgtClick(event, object, value, element) {
      if (this.objectID !== object.id) {
        this.objectID = object.id;
        this.menuVisible = true;
        this.DATA = object;
        this.NODE = value;
      } else {
        this.menuVisible = !this.menuVisible;
      }
      document.addEventListener('click',(e)=>{
        this.menuVisible = false;
      })
      let menu = document.querySelector("#rightClickMenu");
      /* 菜单定位基于鼠标点击位置 */
      menu.style.left = event.clientX + 20 + "px";
      menu.style.top = event.clientY - 30 + "px";
      menu.style.position = "absolute"; // 为新创建的DIV指定绝对定位
      menu.style.width = 160 + "px";
      /*console.log("右键被点击的左侧:",menu.style.left);
        console.log("右键被点击的顶部:",menu.style.top);*/
      //        console.log("右键被点击的event:",event);
      // console.log("右键被点击的object:", object);
      // console.log("右键被点击的value:", value);
      // console.log("右键被点击的element:", element);
    },
    rihgtClick2(event, object, value, element) {
      if (this.objectID2 !== object.id) {
        this.objectID2 = object.id;
        this.menuVisible2 = true;
        this.DATA = object;
        this.NODE = value;
      } else {
        this.menuVisible2 = !this.menuVisible2;
      }
      document.addEventListener('click',(e)=>{
        this.menuVisible2 = false;
      })
      let menu = document.querySelector("#rightClickMenu2");
      /* 菜单定位基于鼠标点击位置 */
      menu.style.left = event.clientX + 30 + "px";
      menu.style.top = event.clientY + 10 + "px";
      menu.style.position = "fixed"; // 为新创建的DIV指定绝对定位
      menu.style.width = 160 + "px";
      // console.log("右键被点击的左侧:",menu.style.left);
      //  console.log("右键被点击的顶部:",menu.style.top);
      // console.log("右键被点击的event:",event);
      //  console.log("右键被点击的object:",object);
      //  console.log("右键被点击的value:",value);
      //  console.log("右键被点击的element:",element);
    },
    dialogNewFormConfirm() {
      (this.dialogNewFormVisible = false),
        this.$message({
          type: "success",
          message: "新建成功!"
        });
    },
    handleNew() {
      this.dialogNewFormVisible = true;
    },
    handleEdit(index, row) {
      this.editObj = row;
      this.dialogFormVisible = true;
      console.log(index, row);
    },
    handleClassifyEdit(index, row) {
      console.log(index);
      console.log(row);
      this.dialogClassifyVisible = true;
      // console.log(index, row);
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    }
  }
};
</script>
<style lang="less" scoped>
.popover-new {
  // position: absolute!important;
  // right: 0;
  // bottom: 0;
}
/*顶部按钮*/
	.slot-tree .slot-t-top{
		margin-bottom: 15px;
	}
	.slot-tree .slot-t-node span{
		display: inline-block;
	}
	/*节点*/
	.slot-tree .slot-t-node--label{
		font-weight: 600;
	}
	/*输入框*/
	.slot-tree .slot-t-input .el-input__inner{
		// height: 26px;
		// line-height: 26px;
	}
	/*按钮列表*/
	.slot-tree .slot-t-node .slot-t-icons{
		display: none;
		margin-left: 10px;
	}
	.slot-tree .slot-t-icons .el-button+.el-button{
		margin-left: 6px;
	}
	.slot-tree .el-tree-node__content:hover .slot-t-icons{
		display: inline-block;
	}
</style>

