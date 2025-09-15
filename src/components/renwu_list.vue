<template>
  <div class="app-container">
    <!-- 顶部状态栏 -->
    <div class="status-bar">
      <div class="status-left">
        <span class="time">19:22</span>
        <span class="network">4G</span>
        <span class="speed">0.38 KB/S</span>
        <span class="hd">HD</span>
      </div>
      <div class="status-right">
        <span class="battery">80</span>
      </div>
    </div>

    <!-- 固定头部导航栏 -->
    <div class="sticky-header">
      <!-- 导航栏 -->
        <div class="nav-bar">
          <div class="nav-left">
            <img src="../assets/Arrow_black.png" alt="返回" class="back-arrow-img">
          </div>
        </div>

      <!-- 排序和筛选 -->
      <div class="filter-bar">
        <div class="sort-section">
          <span class="sort-text">排序</span>
          <img src="../assets/Arrow_gray.png" alt="箭头" class="filter-arrow-img">
        </div>
        <div class="filter-section">
          <span class="filter-text">筛选</span>
          <img src="../assets/Arrow_gray.png" alt="箭头" class="filter-arrow-img">
        </div>
      </div>
    </div>

    <!-- 预约记录列表 -->
    <div class="booking-list">
      <div v-for="(booking, index) in bookingList" :key="booking.id" class="booking-card">
        <div class="booking-header">
          <div class="booking-info">
            <div class="info-row">
              <span class="label">单据编号:</span>
              <span class="value">{{ booking.documentNumber }}</span>
            </div>
            <div class="info-row">
              <span class="label">订单状态:</span>
              <span class="value">{{ booking.status }}</span>
            </div>
            <div class="info-row">
              <span class="label">所属部门:</span>
              <span class="value">{{ booking.department }}</span>
            </div>
            <div class="info-row">
              <span class="label">预约人:</span>
              <span class="value">{{ booking.booker }}</span>
            </div>
            <div class="info-row">
              <span class="label">手机号:</span>
              <span class="value">{{ booking.phone }}</span>
            </div>
            <div class="info-row">
              <span class="label">场地类型:</span>
              <span class="value">{{ booking.venueType }}</span>
            </div>
            <div class="info-row">
              <span class="label">预约场地:</span>
              <span class="value">{{ booking.bookedVenue }}</span>
            </div>
            <div class="info-row">
              <span class="label">备注:</span>
              <span class="value">{{ booking.notes }}</span>
            </div>
            <div class="info-row">
              <span class="label">总计金额:</span>
              <span class="value">{{ booking.totalAmount }}</span>
            </div>
            <div class="info-row">
              <span class="label">创建时间:</span>
              <span class="value">{{ booking.createTime }}</span>
            </div>
            <div class="info-row" @click="toggleDetails(index)">
              <span class="label">预约明细:</span>
              <span class="value">{{ booking.showDetails ? '收起数据' : '展开数据' }}</span>
              <img src="../assets/Arrow_gray.png" alt="箭头" class="detail-arrow-img" :class="{ 'rotated': booking.showDetails }">
            </div>
            
          </div>
          <div class="expanded-details" v-if="booking.showDetails">
            <div class="detail-row">
              <span class="label">预约日期:</span>
              <span class="value">{{ booking.appointmentDate }}</span>
            </div>
            <div class="detail-row">
              <span class="label">开始时间:</span>
              <span class="value">{{ booking.startTime }}</span>
            </div>
            <div class="detail-row">
              <span class="label">结束时间:</span>
              <span class="value">{{ booking.endTime }}</span>
            </div>
            <div class="detail-row">
              <span class="label">价格:</span>
              <span class="value">{{ booking.price }}</span>
            </div>
            <div class="detail-row">
              <span class="label">状态:</span>
              <span class="value">{{ booking.appointmentStatus }}</span>
            </div>
          </div>
        </div>
		<div class="qr-button-section">
		  <button class="qr-button-standalone" @click="viewQRCode(booking)">查看二维码</button>
		</div>
      </div>
    </div>

    <!-- 添加按钮 -->
    <div class="add-button" @click="addNewBooking">
      <span class="plus">+</span>
    </div>

    <!-- 查看二维码弹窗 -->
    <div v-if="showQRModal" class="modal-overlay" @click="closeQRModal">
      <div class="qr-modal-content" @click.stop>
        <div class="qr-code-card">
          <img src="../assets/1.jpg" alt="二维码" class="qr-code-image">
        </div>
      </div>
    </div>

    <!-- 添加预约弹窗 -->
    <div v-if="showAddModal" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h3>添加预约记录</h3>
          <span class="close-btn" @click="closeModal">×</span>
        </div>
        <div class="modal-body">
          <div class="form-group">
            <label>预约人:</label>
            <input v-model="newBooking.booker" type="text" placeholder="请输入预约人姓名">
          </div>
          <div class="form-group">
            <label>手机号:</label>
            <input v-model="newBooking.phone" type="text" placeholder="请输入手机号">
          </div>
          <div class="form-group">
            <label>场地类型:</label>
            <select v-model="newBooking.venueType">
              <option value="健身房">健身房</option>
              <option value="篮球场">篮球场</option>
              <option value="羽毛球场">羽毛球场</option>
            </select>
          </div>
          <div class="form-group">
            <label>预约场地:</label>
            <input v-model="newBooking.bookedVenue" type="text" placeholder="请输入预约场地">
          </div>
          <div class="form-group">
            <label>预约日期:</label>
            <input v-model="newBooking.appointmentDate" type="date" placeholder="请选择预约日期">
          </div>
          <div class="form-group">
            <label>开始时间:</label>
            <input v-model="newBooking.startTime" type="time" placeholder="请选择开始时间">
          </div>
          <div class="form-group">
            <label>结束时间:</label>
            <input v-model="newBooking.endTime" type="time" placeholder="请选择结束时间">
          </div>
          <div class="form-group">
            <label>价格:</label>
            <input v-model="newBooking.price" type="number" placeholder="请输入价格" min="0">
          </div>
          <div class="form-group">
            <label>备注:</label>
            <textarea v-model="newBooking.notes" placeholder="请输入备注信息"></textarea>
          </div>
          <div class="form-group">
            <label>添加预约二维码:</label>
            <div class="qr-upload-section">
              <input type="file" accept="image/*" @change="handleQRUpload" style="display: none;" ref="qrFileInput">
              <button type="button" class="qr-upload-btn" @click="$refs.qrFileInput.click()">
                {{ newBooking.qrCode ? '已上传二维码' : '点击上传二维码' }}
              </button>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn-cancel" @click="closeModal">取消</button>
          <button class="btn-confirm" @click="confirmAdd">确认添加</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, reactive } from 'vue'

export default {
  name: 'BookingLog',
  setup() {
    const showAddModal = ref(false)
    const showQRModal = ref(false)
    const bookingList = ref([
      {
        id: 1,
        documentNumber: 'YY202509256000022412001',
        status: '已支付',
        department: '广州松田职业学院/教学部门/信息工程学院',
        booker: '黄镇科/202311008971',
        phone: '',
        venueType: '健身房',
        bookedVenue: '健身房',
        notes: '',
        totalAmount: '0.00',
        createTime: '2025-09-13 00:00:21',
        appointmentDate: '2025-09-13',
        startTime: '14:00:00',
        endTime: '15:00:00',
        price: '0',
        appointmentStatus: '正常',
        showDetails: false
      },
      {
        id: 2,
        documentNumber: 'YY202509256000017696001',
        status: '已支付',
        department: '广州松田职业学院/教学部门/信息工程学院',
        booker: '黄镇科/202311008971',
        phone: '',
        venueType: '健身房',
        bookedVenue: '健身房',
        notes: '',
        totalAmount: '0.00',
        createTime: '2025-09-12 15:30:45',
        appointmentDate: '2025-09-12',
        startTime: '16:00:00',
        endTime: '17:00:00',
        price: '0',
        appointmentStatus: '正常',
        showDetails: false
      }
    ])

    const newBooking = reactive({
      booker: '',
      phone: '',
      venueType: '健身房',
      bookedVenue: '',
      notes: '',
      appointmentDate: '',
      startTime: '',
      endTime: '',
      price: '0',
      qrCode: null
    })

    const toggleDetails = (index) => {
      bookingList.value[index].showDetails = !bookingList.value[index].showDetails
    }

    const viewQRCode = (booking) => {
      showQRModal.value = true
    }

    const closeQRModal = () => {
      showQRModal.value = false
    }

    const handleQRUpload = (event) => {
      const file = event.target.files[0]
      if (file) {
        newBooking.qrCode = file
      }
    }

    const addNewBooking = () => {
      showAddModal.value = true
    }

    const closeModal = () => {
      showAddModal.value = false
      // 重置表单
      Object.assign(newBooking, {
        booker: '',
        phone: '',
        venueType: '健身房',
        bookedVenue: '',
        notes: '',
        appointmentDate: '',
        startTime: '',
        endTime: '',
        price: '0',
        qrCode: null
      })
    }

    const confirmAdd = () => {
      if (!newBooking.booker.trim()) {
        alert('请输入预约人姓名')
        return
      }
      if (!newBooking.appointmentDate) {
        alert('请选择预约日期')
        return
      }
      if (!newBooking.startTime) {
        alert('请选择开始时间')
        return
      }
      if (!newBooking.endTime) {
        alert('请选择结束时间')
        return
      }

      const newId = Math.max(...bookingList.value.map(b => b.id)) + 1
      const now = new Date()
      const createTime = now.getFullYear() + '-' + 
        String(now.getMonth() + 1).padStart(2, '0') + '-' + 
        String(now.getDate()).padStart(2, '0') + ' ' + 
        String(now.getHours()).padStart(2, '0') + ':' + 
        String(now.getMinutes()).padStart(2, '0') + ':' + 
        String(now.getSeconds()).padStart(2, '0')

      const newRecord = {
        id: newId,
        documentNumber: `YY${now.getFullYear()}${String(now.getMonth() + 1).padStart(2, '0')}${String(now.getDate()).padStart(2, '0')}${String(now.getTime()).slice(-6)}001`,
        status: '已支付',
        department: '广州松田职业学院/教学部门/信息工程学院',
        booker: newBooking.booker,
        phone: newBooking.phone,
        venueType: newBooking.venueType,
        bookedVenue: newBooking.bookedVenue || newBooking.venueType,
        notes: newBooking.notes,
        totalAmount: newBooking.price + '.00',
        createTime: createTime,
        appointmentDate: newBooking.appointmentDate,
        startTime: newBooking.startTime,
        endTime: newBooking.endTime,
        price: newBooking.price,
        appointmentStatus: '正常',
        showDetails: false
      }

      bookingList.value.unshift(newRecord)
      closeModal()
    }

    return {
      showAddModal,
      showQRModal,
      bookingList,
      newBooking,
      toggleDetails,
      viewQRCode,
      closeQRModal,
      handleQRUpload,
      addNewBooking,
      closeModal,
      confirmAdd
    }
  }
}
</script>

<style scoped>
.app-container {
  max-width: 430px;
  margin: 0 auto;
  background: #f5f5f5;
  min-height: 100vh;
  position: relative;
  font-family: "SimHei", "Microsoft YaHei", "黑体", Arial, sans-serif;
}

/* 状态栏 */
.status-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px;
  background: #000;
  color: #fff;
  font-size: 14px;
  font-weight: 500;
}

.status-left {
  display: flex;
  align-items: center;
  gap: 8px;
}

.status-right {
  display: flex;
  align-items: center;
  gap: 8px;
}

.battery {
  font-weight: bold;
}

/* 固定头部导航栏 */
.sticky-header {
  position: sticky;
  top: 0;
  z-index: 1000;
  background: #fff;
}

/* 导航栏 */
.nav-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  background: #fff;
}

.nav-left, .nav-right {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
  color: #333;
}

.back-arrow-img {
  width: 20px;
  height: 20px;
}

.nav-center {
  flex: 1;
  text-align: center;
  font-size: 12px;
  color: #666;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* 筛选栏 */
.filter-bar {
  display: flex;
  background: #fff;
  padding: 8px 16px;
}

.sort-section, .filter-section {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.sort-text, .filter-text {
  font-size: 14px;
  color: #333;
  font-weight: 400;
  cursor: pointer;
}

.filter-arrow-img {
  width: 12px;
  height: 12px;
  margin-left: 4px;
  transform: rotate(90deg);
}

.detail-arrow-img {
  width: 12px;
  height: 12px;
  margin-left: 4px;
  transition: transform 0.3s ease;
}

.detail-arrow-img.rotated {
  transform: rotate(90deg);
}

.arrow {
  font-size: 10px;
  color: #999;
}

/* 预约列表 */
.booking-list {
  padding: 16px;
  padding-bottom: 80px;
  padding-top: 0;
}

.booking-card {
  background: #fff;
  border-radius: 8px;
  margin-bottom: 12px;
  overflow: hidden;
}

.booking-header {
  padding: 16px;
}

.booking-info {
  margin-bottom: 16px;
}

.info-row {
  display: flex;
  padding: 14px 0;
  font-size: 16px;
  line-height: 1.6;
  border-bottom: 1px solid #f0f0f0;
  align-items: flex-start;
  min-height: 22px;
}

.info-row:last-child {
  border-bottom: 1px solid #f0f0f0;
}

.info-row.expandable {
  cursor: pointer;
  padding: 14px 0;
  border-top: 1px solid #f0f0f0;
  margin-top: 8px;
  margin-bottom: 0;
  position: relative;
  display: flex;
  align-items: center;
}

.info-row.expandable .label {
  min-width: 80px;
  flex-shrink: 0;
}

.info-row.expandable .value {
  flex: 1;
  margin: 0 8px;
  margin-left: 8px;
}

.info-row.expandable .arrow {
  margin-right: auto;
}

.qr-button-section {
  padding: 12px 0;
  text-align: right;
  border-bottom: 1px solid #f0f0f0;
}

.qr-button-standalone {
  background: #348ee1;
  color: #fff;
  border: none;
  padding: 8px 16px;
  border-radius: 2px;
  font-size: 16px;
  cursor: pointer;
  font-weight: 400;
  margin-right: 20px;
}

.label {
  color: #333;
  min-width: 90px;
  flex-shrink: 0;
  font-weight: normal;
  text-align: right;
}

.value {
  color: #666;
  flex: 1;
  word-break: break-all;
  margin-left: 8px;
  font-weight: normal;
  text-align: left;
}

.status-paid {
  color: #52c41a;
  font-weight: 500;
}

/* 展开详情样式 */
.expanded-details {
  margin-top: 0;
  padding-top: 0;
  border-top: 1px solid #f0f0f0;
}

.detail-row {
  display: flex;
  align-items: center;
  padding: 14px 0;
  font-size: 14px;
  line-height: 1.4;
  border-bottom: 1px solid #f0f0f0;
}

.detail-row:last-child {
  border-bottom: none;
}

.detail-label {
  color: #666;
  min-width: 80px;
  flex-shrink: 0;
}

.detail-value {
  color: #333;
  flex: 1;
  margin-right: 8px;
}

.status-normal {
  color: #52c41a;
  font-weight: 500;
}

.qr-button-standalone:hover {
  background: #40a9ff;
}

/* 添加按钮 */
.add-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 56px;
  height: 56px;
  background: rgba(24, 144, 255, 0.5);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(24, 144, 255, 0.3);
  z-index: 1000;
  transition: all 0.3s ease;
}

.add-button:hover {
  background: rgba(24, 144, 255, 0.7);
  transform: scale(1.05);
}

.plus {
  color: #fff;
  font-size: 24px;
  font-weight: bold;
}

/* 弹窗样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
}

.modal-content {
  background: #fff;
  border-radius: 8px;
  width: 90%;
  max-width: 400px;
  max-height: 80vh;
  overflow-y: auto;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
  border-bottom: 1px solid #e0e0e0;
}

.modal-header h3 {
  margin: 0;
  font-size: 18px;
  color: #333;
}

.close-btn {
  font-size: 24px;
  color: #999;
  cursor: pointer;
  line-height: 1;
}

.modal-body {
  padding: 16px;
}

.form-group {
  margin-bottom: 16px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-size: 14px;
  color: #333;
  font-weight: 500;
}

.form-group input,
.form-group select,
.form-group textarea {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #d9d9d9;
  border-radius: 4px;
  font-size: 14px;
  box-sizing: border-box;
}

.form-group textarea {
  height: 80px;
  resize: vertical;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
  padding: 16px;
  border-top: 1px solid #e0e0e0;
}

.btn-cancel,
.btn-confirm {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
}

.btn-cancel {
  background: #f5f5f5;
  color: #333;
}

.btn-confirm {
  background: #1890ff;
  color: #fff;
}

.btn-cancel:hover {
  background: #e6e6e6;
}

.btn-confirm:hover {
  background: #40a9ff;
}

/* 二维码弹窗样式 */
.qr-modal-content {
  background: transparent;
  border-radius: 0;
  width: 300px;
  height: 350px;
  overflow: visible;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.qr-code-card {
  background: #fff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.qr-code-image {
  width: 200px;
  height: 200px;
  border-radius: 8px;
  object-fit: contain;
}


/* 二维码上传样式 */
.qr-upload-section {
  display: flex;
  align-items: center;
}

.qr-upload-btn {
  background: #f5f5f5;
  color: #333;
  border: 1px solid #d9d9d9;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  width: 100%;
  text-align: center;
}

.qr-upload-btn:hover {
  background: #e6e6e6;
}

/* 响应式设计 */
@media (max-width: 375px) {
  .app-container {
    max-width: 100%;
  }
  
  .booking-list {
    padding: 12px;
  }
  
  .booking-card {
    margin-bottom: 8px;
  }
  
  .booking-header {
    padding: 12px;
  }
}

/* 移动端默认字体恢复与可读性（覆盖上方局部字号） */
.app-container,
.app-container * {
  font-size: 12px;
  line-height: 1.4;
}
</style>