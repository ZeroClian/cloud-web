<template>
  <div class="main-content">
    <div class="main-content-inner">
      <div class="page-content">
        <div class="row">
          <div class="col-xs-12">
            <!-- PAGE CONTENT BEGINS -->
            <div class="row">
              <div class="col-xs-12">
                <h3 class="header smaller lighter blue">股票详情</h3>
                <p>
                  <button @click="add()" class="btn btn-white btn-info btn-round btn-bold">
                    <i class="ace-icon fa fa-floppy-o blue"></i>
                    新增
                  </button>
                  <button @click="list()" class="btn btn-white btn-default btn-round">
                    <i class="ace-icon fa fa-refresh"></i>
                    刷新
                  </button>
                </p>

                <div id="form-modal" class="modal fade" tabindex="-1" role="dialog">
                  <div class="modal-dialog" role="document">
                    <div class="modal-content">
                      <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span
                            aria-hidden="true">&times;</span></button>
                        <h4 class="modal-title">新增</h4>
                      </div>
                      <div class="modal-body">
                        <form class="form-horizontal">
                          <div class="form-group">
                            <label class="col-sm-2 control-label">股票代码</label>
                            <div class="col-sm-10">
                              <input v-model="stock.code" class="form-control" placeholder="股票代码">
                            </div>
                          </div>
                          <div class="form-group">
                            <label class="col-sm-2 control-label">成本</label>
                            <div class="col-sm-10">
                              <input v-model="stock.cost" class="form-control" placeholder="00.000">
                            </div>
                          </div>
                          <div class="form-group">
                            <label class="col-sm-2 control-label">数量</label>
                            <div class="col-sm-10">
                              <input v-model="stock.quantity" class="form-control" placeholder="手数">
                            </div>
                          </div>
                        </form>
                      </div>
                      <div class="modal-footer">
                        <button type="button" class="btn btn-default" data-dismiss="modal">取消</button>
                        <button type="button" class="btn btn-primary" @click="save()">保存</button>
                      </div>
                    </div><!-- /.modal-content -->
                  </div><!-- /.modal-dialog -->
                </div><!-- /.modal -->

                <table id="simple-table" class="table  table-bordered table-hover">
                  <thead>
                  <tr>
                    <th>股票名称</th>
                    <th>
                      <i class="ace-icon fa fa-clock-o bigger-110 hidden-480"></i>
                      涨跌幅
                    </th>
                    <th>现价</th>
                    <th class="hidden-480">成本</th>
                    <th class="hidden-480">数量</th>
                    <th class="hidden-480">盈亏</th>
                  </tr>
                  </thead>

                  <tbody>
                  <tr v-for="stock in stocks">
                    <td>{{ stock.name }}</td>
                    <td>{{ stock.change }}</td>
                    <td>{{ stock.currentPrice }}</td>
                    <td>{{ stock.cost }}</td>
                    <td>{{ stock.quantity }}</td>
                    <td>
                      <span v-if="stock.profit > 0"
                            class="label label-danger arrowed-in-right arrowed">{{ stock.profit }}</span>
                      <span v-if="stock.profit === 0"
                            class="label label-inverse arrowed-in-right arrowed">{{ stock.profit }}</span>
                      <span v-if="stock.profit < 0"
                            class="label label-success arrowed-in-right arrowed">{{ stock.profit }}</span>
                    </td>

                    <td>
                      <div class="hidden-sm hidden-xs btn-group">
                        <button @click="edit(stock)" class="btn btn-xs btn-info">
                          <i class="ace-icon fa fa-pencil bigger-120"></i>
                        </button>

                        <button @click="del(stock.id)" class="btn btn-xs btn-danger">
                          <i class="ace-icon fa fa-trash-o bigger-120"></i>
                        </button>
                      </div>

                      <div class="hidden-md hidden-lg">
                        <div class="inline pos-rel">
                          <button class="btn btn-minier btn-primary dropdown-toggle"
                                  data-toggle="dropdown" data-position="auto">
                            <i class="ace-icon fa fa-cog icon-only bigger-110"></i>
                          </button>

                          <ul class="dropdown-menu dropdown-only-icon dropdown-yellow dropdown-menu-right dropdown-caret dropdown-close">
                            <li>
                              <a href="#" class="tooltip-info" data-rel="tooltip"
                                 title="View">
																			<span class="blue">
																				<i class="ace-icon fa fa-search-plus bigger-120"></i>
																			</span>
                              </a>
                            </li>

                            <li>
                              <a href="#" class="tooltip-success" data-rel="tooltip"
                                 title="Edit">
																			<span class="green">
																				<i class="ace-icon fa fa-pencil-square-o bigger-120"></i>
																			</span>
                              </a>
                            </li>

                            <li>
                              <a href="#" class="tooltip-error" data-rel="tooltip"
                                 title="Delete">
																			<span class="red">
																				<i class="ace-icon fa fa-trash-o bigger-120"></i>
																			</span>
                              </a>
                            </li>
                          </ul>
                        </div>
                      </div>
                    </td>
                  </tr>
                  </tbody>
                </table>
              </div><!-- /.span -->
            </div><!-- /.row -->
            <!-- PAGE CONTENT ENDS -->
          </div><!-- /.col -->
        </div><!-- /.row -->
      </div><!-- /.page-content -->
    </div>
  </div><!-- /.main-content -->
</template>

<script>
export default {
  name: "finance-stock",
  data: function () {
    return {
      stock: {
        code: "",
        cost: "",
        quantity: ""
      },
      stocks: []
    }
  },
  mounted: function () {
    let _this = this
    _this.list()
  },
  methods: {
    add() {
      let _this = this;
      _this.stock = {};
      $("#form-modal").modal("show");
    },
    edit(stock) {
      let _this = this;
      _this.stock = $.extend({}, stock);
      $("#form-modal").modal("show");
    },
    del(id) {
      let _this = this;
      Confirm.show("确认删除股票,删除后不可恢复?", function () {
        _this.$ajax.delete(process.env.VUE_APP_SERVER + '/service/stock/del/' + id).then((respond) => {
          if (respond.data.success) {
            _this.list();
            Toast.success("删除成功!")
          }
        })
      })
    },
    list() {
      let _this = this
      _this.$ajax.get(process.env.VUE_APP_SERVER + "/service/stock/list").then((respond) => {
        console.log(respond)
        _this.stocks = respond.data.data
      })
    },
    save() {
      let _this = this
      // 校验
      if (!Validator.require(_this.stock.code, "股票代码") ||
          !Validator.require(_this.stock.cost, "成本") ||
          !Validator.require(_this.stock.quantity, "数量")) {
        return
      }
      _this.$ajax.post(process.env.VUE_APP_SERVER + '/service/stock/add', _this.stock).then((respond) => {
        console.log(respond.data.data);
        if (respond.data.data) {
          $("#form-modal").modal("hide");
          _this.list();
          Toast.success("保存成功!");
        } else {
          Toast.error(respond.data.message);
        }
      })
    },
  }
}
</script>

<style scoped>

</style>