<template>
  <el-dialog
    class="vipDialog"
    title="思绪会员"
    :visible.sync="dialogVisible"
    width="800px"
    @close="onClose"
  >
    <div class="vipBox" :class="{ isDark: isDark }">
      <h1 :class="{ isVIP: isVIP }" @click="showParty" ref="titleRef">
        {{ vipString }}
      </h1>
      <div class="expireTime" v-if="expireTime">到期时间：{{ expireTime }}</div>
      <div class="btnList" v-if="!isVIP">
        <div class="vipButton" @click="openPay">
          <span class="text">点此开通终身会员</span>
        </div>
        <div class="vipButton minthVipButton" @click="openMonthPay">
          <span class="text">先开一个月试试</span>
        </div>
      </div>
      <h2>会员专享如下功能</h2>
      <div class="tableBox">
        <el-table :data="functionList" style="width: 100%;" height="450" stripe>
          <el-table-column
            type="index"
            width="50"
            :index="indexMethod"
          ></el-table-column>
          <el-table-column prop="name" label="功能" width="150">
          </el-table-column>
          <el-table-column prop="desc" label="描述"> </el-table-column>
          <el-table-column prop="img" label="截图">
            <template slot-scope="scope">
              <img
                style="width: 200px;"
                :src="scope.row.img"
                alt=""
                @click="previewImg(scope.row.img)"
              />
            </template>
          </el-table-column>
        </el-table>
      </div>
    </div>
  </el-dialog>
</template>

<script>
import { mapState, mapMutations } from 'vuex'
import img1 from '@/assets/img/vip/1.png'
import img2 from '@/assets/img/vip/2.jpg'
import img3 from '@/assets/img/vip/3.jpg'
import img4 from '@/assets/img/vip/4.png'
import img5 from '@/assets/img/vip/5.png'
import img6 from '@/assets/img/vip/6.png'
import img7 from '@/assets/img/vip/7.gif'
import img8 from '@/assets/img/vip/8.gif'
import party from 'party-js'

const functionList = [
  {
    name: '手绘风格',
    desc:
      '开启后思维导图连线和形状会显示为手绘风格样式。可以在【设置】里开启或关闭。',
    img: img1
  },
  {
    name: '节点标记',
    desc:
      '可以标记单个节点，也就是可以在单个节点上加一个手绘风格的圈、背景、删除线等等，支持动画效果。',
    img: img2
  },
  {
    name: '节点编号',
    desc: '可以一键编号某个节点的子节点，支持多种编号形式，支持编号指定层级。',
    img: img3
  },
  {
    name: '导出Freemind文件',
    desc: '可以导入和导出Freemind软件的格式，即.mm文件格式。',
    img: img4
  },
  {
    name: '导出Excel文件',
    desc: '可以导入和导出Excel软件的格式，即.xlsx文件格式。',
    img: img5
  },
  {
    name: '节点待办',
    desc:
      '支持给节点添加待办，即可以给节点添加一个勾选框，点击勾选框可以切换完成和未完成的状态。',
    img: img6
  },
  {
    name: '节点连线流动效果',
    desc: '可以给节点连线添加流动效果，仅在连线为虚线的情况下生效。',
    img: img7
  },
  {
    name: '动量效果',
    desc:
      '开启后，鼠标按住拖动画布，然后松开后画布会根据惯性继续移动一段距离。可以在【设置】里开启或关闭。',
    img: img8
  },
  {
    name: '更多功能...',
    desc: '新功能持续开发中，敬请期待。',
    img: ''
  }
]

export default {
  model: {
    prop: 'value',
    event: 'change'
  },
  props: {
    value: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      dialogVisible: false,
      functionList,
      expireTime: ''
    }
  },
  computed: {
    ...mapState({
      isDark: state => state.localConfig.isDark,
      isVIP: state => state.isVIP
    }),

    vipString() {
      return this.isVIP
        ? this.expireTime
          ? '恭喜你，你是月度会员~'
          : '恭喜你，你已是终身会员~'
        : '终身会员，现在早鸟价，仅需：￥100'
    }
  },
  watch: {
    value(val) {
      this.dialogVisible = val
      if (val) {
        this.checkIsVip()
      }
    }
  },
  methods: {
    ...mapMutations(['setIsVIP']),

    checkIsVip() {
      const purchasedUser = utools.isPurchasedUser()
      if (purchasedUser && typeof purchasedUser === 'string') {
        this.expireTime = purchasedUser
      }
    },

    onClose() {
      this.$emit('change', false)
    },

    indexMethod(index) {
      return index + 1
    },

    openPay() {
      utools.openPurchase(
        { goodsId: 'hwtmnyxa5grdr3tlaj1gkkfpguff4cmk' },
        () => {
          this.setIsVIP(true)
          this.showParty()
        }
      )
    },

    openMonthPay() {
      utools.openPurchase(
        { goodsId: '8w2vkwxaaht9lvt3b0q338m0mxwflvu1' },
        () => {
          this.setIsVIP(true)
          this.checkIsVip()
          this.showParty()
        }
      )
    },

    previewImg(img) {
      this.$viewerApi({
        images: [img]
      })
    },

    showParty() {
      if (!this.isVIP) return
      party.confetti(this.$refs.titleRef, {
        count: party.variation.range(20, 40)
      })
    }
  }
}
</script>

<style lang="less" scoped>
.vipDialog {
  /deep/ .el-dialog {
    border-radius: 10px;
    background-image: linear-gradient(
      to right,
      rgb(252, 234, 215),
      rgb(248, 213, 189)
    );

    .el-dialog__title {
      display: none;
    }

    .el-dialog__body {
      padding: 0;
      background-color: transparent !important;
    }
  }
}

.vipBox {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px;

  &.isDark {
  }

  .expireTime {
    font-size: 14px;
    margin-top: -20px;
    margin-bottom: 20px;
    color: rgb(145, 90, 66);
  }

  h1 {
    color: rgb(21, 12, 6);
    line-height: 50px;
    margin-bottom: 20px;
    user-select: none;

    &.isVIP {
      color: rgb(145, 90, 66);
      cursor: pointer;
    }
  }

  h2 {
    color: rgb(74, 60, 50);
  }

  .btnList {
    display: flex;
    align-items: center;
  }

  .vipButton {
    display: flex;
    align-items: center;
    border: 1px solid rgb(251, 231, 171);
    height: 35px;
    padding: 0 12px;
    border-radius: 15px;
    font-weight: bold;
    cursor: pointer;
    transition: all 1s;
    transform: rotateX(0);
    background-color: rgb(51, 45, 38);
    color: rgb(248, 207, 143);
    margin-top: 12px;
    transition: all 0.3s;
    user-select: none;
    margin-bottom: 20px;

    &:active {
      transform: translateY(-2px);
    }

    &.minthVipButton {
      margin-left: 12px;
    }
  }

  .tableBox {
    width: 100%;
    border: 1px solid rgb(145, 90, 66);
    border-radius: 5px;
    overflow: hidden;

    img {
      cursor: pointer;
    }

    /deep/ .el-table {
      background-color: transparent !important;
      color: rgb(145, 90, 66);
      font-weight: bold;

      thead {
        color: rgb(74, 60, 50);
      }

      tr {
        background-color: transparent;
      }

      th.el-table__cell {
        background-color: rgba(255, 255, 255, 0.5) !important;
      }

      td.el-table__cell,
      th.el-table__cell.is-leaf {
        border-color: rgba(255, 255, 255, 0.5);
      }

      &.el-table--striped
        .el-table__body
        tr.el-table__row--striped
        td.el-table__cell {
        background: rgb(249, 223, 197);
      }

      &.el-table--enable-row-hover
        .el-table__body
        tr:hover
        > td.el-table__cell {
        background-color: rgb(214, 178, 143);
      }
    }
  }
}
</style>
