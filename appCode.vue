<template data-edit-id="1">
  <div class="tw-relative tw-px-3 tw-pt-3 wizard-bg tw-flex tw-flex-col tw-items-center tw-w-full tw-h-full" ref="rootComponentContainer" data-edit-id="2">
    <div v-if="(isSolo || content.isSolo) && dataEditable" class="wizard-navbar tw-flex tw-items-center tw-justify-between tw-max-w-7xl tw-mx-auto tw-w-full" data-edit-id="100">
      <div :style="{ width: flag && step !== 0 ? '100%' : '' }" class="tw-flex tw-items-center tw-gap-3 logoInfoArea" data-edit-id="101">
        <h3 class="tw-text-2xl tw-text-gray-900 tw-tracking-tight tw-m-0 tw-font-medium logo-title tw-w-full" data-edit-id="102">
          <slot v-if="step === 0" data-edit-id="292">
            <div class="logo-badge tw-flex tw-items-center tw-justify-center" data-edit-id="409">
              <span class="logo-badge-icon" data-edit-id="410">{{
                icon || "∑"
              }}</span>
            </div>
            <span class="logo-text" data-edit-id="242">
              <span class="title-text" data-edit-id="368">{{ title }}</span>
              <span class="slogn-text" data-edit-id="369">{{ introduce }}</span>
            </span>
          </slot>
          <slot v-else data-edit-id="308">
            <span title="返回" class="goBackBtn" @click="goBack()" data-edit-id="295">
              <el-icon data-edit-id="384">
                <arrow-left-bold data-edit-id="385" />
              </el-icon>
            </span>
            <slot v-if="
                accountData && Object.keys(accountData).length && !isStreaming
              " data-edit-id="357">
              <el-dropdown @command="handleContentCommand" style="vertical-align: middle" :teleported="false" append-to="parent" trigger="click" data-edit-id="400">
                <el-button text type="primary" data-edit-id="312">
                  操作
                  <el-icon class="el-icon--right" data-edit-id="402">
                    <arrow-down data-edit-id="403" />
                  </el-icon>
                </el-button>

                <template #dropdown>
                  <el-dropdown-menu>
                    <el-dropdown-item command="htmlDownload">
                      存档 HTML
                    </el-dropdown-item>

                    <el-dropdown-item command="markdownDownload">
                      存档 Markdown
                    </el-dropdown-item>

                    <el-dropdown-item command="imageDownload">
                      导出图片
                    </el-dropdown-item>

                    <el-dropdown-item command="editContent">
                      {{ viewMode ? "查看内容" : "手动修改" }}
                    </el-dropdown-item>

                    <el-dropdown-item command="copyLink">
                      复制链接
                    </el-dropdown-item>

                    <el-dropdown-item command="readLocal">
                      本地读档
                    </el-dropdown-item>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <slot v-if="viewMode">
                <el-tag class="versionIndexSpan">最近第 {{ currentVersionIndex + 1 }} 个版本
                </el-tag>
                <el-button class="startModifyBtn" size="small" text type="primary" @click="startModify">开始修改</el-button>
              </slot>
              <el-input :style="{ width: flag ? '300px' : '300px' }" v-model="modifyInfo" v-else placeholder="输入变更描述..." class="custom-input-group" clearable @keyup.enter="handleConfirm" data-edit-id="342">
                <template #append>
                  <el-button :loading="modifyLoading" :disabled="modifyLoading" style="color: #fff" class="!bg-blue-500 !border-blue-500 !text-white hover:!bg-blue-600 transition-all duration-300 wizard-action-btn wizard-action-btn--primary" @click="handleConfirm">
                    <span class="font-medium px-2">
                      <i class="bi bi-magic"></i>
                    </span>
                  </el-button>
                </template>
              </el-input>
            </slot>
            <span v-else style="font-size: 14px"> {{ title }}</span>
            <span class="versionListArea" style="float: right; margin-right: 10px" v-if="versionList && versionList.length !== 0 && !isStreaming">
              <el-dropdown @command="handleVersionChange" trigger="click" :teleported="false" append-to="parent" style="vertical-align: middle" v-if="step === 1" data-edit-id="400">
                <el-button text type="primary" data-edit-id="312">
                  版本
                  <el-icon class="el-icon--right" data-edit-id="402">
                    <arrow-down data-edit-id="403" />
                  </el-icon>
                </el-button>
                <template #dropdown>
                  <el-dropdown-menu>
                    <slot v-for="(item, index) in versionList">
                      <el-dropdown-item :style="{
                          fontWeight: index === currentVersionIndex ? 800 : 300,
                        }" :command="index" class="tw-justify-between">
                        <slot v-if="index === currentVersionIndex">
                          {{ getVersionLabel(item) }}
                        </slot>
                        <slot v-else>
                          {{ getVersionLabel(item) }}
                          <el-button style="margin-left: 5px" @click.stop="checkDiff(index)" size="small">对比</el-button>
                        </slot>
                      </el-dropdown-item>
                    </slot>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <span v-if="!flag" class="versionLabel">{{
                getVersionLabel(versionList[currentVersionIndex])
              }}</span>
            </span>
          </slot>
        </h3>
      </div>
      <div class="navbar-actions" data-edit-id="103">
        <slot v-if="step === 0 && !flag">
          <el-button class="wizard-action-btn navbar-action-btn" @click="openFeedbackDialog()">
            问题反馈
          </el-button>
        </slot>
        <span class="navbar-divider" aria-hidden="true"></span>
        <div class="navbar-account">
          <div appName="loginRegItem" component="CardItem" :documentId="query ? query.documentId : ''" class="login-bar-card refined-user-card" data-edit-id="104"></div>
        </div>
      </div>
    </div>

    <div ref="loadingEffectArea" style="height: 100%; position: relative; z-index: 0" class="tw-max-w-7xl tw-y-full tw-mx-auto tw-w-full" data-edit-id="3">
      <!-- 输入控制面板 -->
      <div v-if="step === 0" class="wizard-input-panel tw-relative tw-bg-white tw-border tw-border-slate-100 tw-rounded-2xl tw-shadow-sm tw-p-5 tw-transition-all hover:tw-shadow-md" data-edit-id="4">
        <div class="tw-flex tw-flex-col md:tw-flex-row tw-gap-5 tw-items-stretch">
          <div class="tw-flex-1 tw-flex tw-flex-col tw-justify-between tw-min-w-0">
            <div class="input-spell-box tw-w-full" data-edit-id="122">
              <el-input :placeholder="inputPlaceholder" :rows="4" resize="none" v-model="content.inputText" type="textarea" class="feature-input tw-w-full" data-edit-id="131"></el-input>
            </div>

            <div class="tw-flex tw-items-center tw-flex-wrap tw-gap-2 tw-mt-4">
              <span v-if="!flag" class="tw-text-sm tw-text-slate-400 tw-font-medium tw-whitespace-nowrap">
                试试看：
              </span>
              <button v-for="d in exampleList" :key="d.title || d.name" @click="inputExampleText(d)" class="example-chip tw-group tw-flex tw-items-center tw-gap-1.5 tw-px-3 tw-py-1.5 tw-text-[13px] tw-bg-slate-50 hover:tw-bg-blue-50 hover:tw-text-blue-600 tw-text-slate-600 tw-rounded-lg tw-transition-colors tw-whitespace-nowrap active:tw-scale-95">
                <span class="chip-icon">{{ d.icon || "" }}</span>
                <span>{{ d.name }}</span>
              </button>
            </div>
          </div>

          <div class="tw-h-px md:tw-hidden tw-bg-slate-100 tw-w-full"></div>
          <div class="tw-hidden md:tw-block tw-w-px tw-bg-slate-100 tw-my-1 tw-self-stretch"></div>

          <div class="aigcOptionArea tw-flex tw-flex-row md:tw-flex-col tw-justify-between md:tw-justify-end tw-gap-3 tw-w-full md:tw-w-32 tw-shrink-0">
            <el-button :style="{ marginTop: flag ? '5px' : '' }" type="primary" :disabled="!content.inputText && !description" :loading="loading" @click="handleGenerate" class="cast-spell-btn tw-flex-1 md:tw-flex-none tw-mt-6 tw-h-10 tw-shadow-sm" data-edit-id="11">
              <i class="bi bi-lightning-charge-fill tw-mr-1.5" data-edit-id="12"></i>
              立即生成
            </el-button>

            <el-button v-if="!flag" text type="info" :loading="readingFile" :disabled="readingFile" @click="triggerFileInput" class="tw-flex-1 md:tw-flex-none tw-justify-center tw-h-10 hover:!tw-bg-slate-50 tw-text-slate-500 tw-mt-8">
              <i class="bi bi-folder2-open tw-mr-1"></i>
              本地读档
            </el-button>
          </div>
        </div>
      </div>
      <span v-if="!(isSolo || content.isSolo) && step === 1 && dataEditable" style="
          float: right;
          position: relative;
          width: 100%;
          text-align: right;
          margin-top: 10px;
          margin-bottom: 3px;
          right: 0px;
          display: inline-block;
        " data-edit-id="357">
        <span title="返回" class="goBackBtn" @click="goBack()" data-edit-id="295">
          <el-icon data-edit-id="384">
            <arrow-left-bold data-edit-id="385" />
          </el-icon>
        </span>
        <span class="versionListArea" style="float: left" v-if="versionList && versionList.length !== 0 && !isStreaming">
          <el-dropdown @command="handleVersionChange" trigger="click" :teleported="false" append-to="parent" v-if="step === 1" data-edit-id="400">
            <el-button text type="primary" data-edit-id="312">
              版本
              <el-icon class="el-icon--right" data-edit-id="402">
                <arrow-down data-edit-id="403" />
              </el-icon>
            </el-button>
            <template #dropdown>
              <el-dropdown-menu>
                <slot v-for="(item, index) in versionList">
                  <el-dropdown-item class="tw-justify-between" :command="index" :style="{
                      fontWeight: index === currentVersionIndex ? 800 : 300,
                    }">
                    <slot v-if="index === currentVersionIndex">
                      {{ getVersionLabel(item) }}
                    </slot>
                    <slot v-else>
                      {{ getVersionLabel(item) }}
                      <el-button style="margin-left: 5px" @click.stop="checkDiff(index)" size="small">对比</el-button>
                    </slot>
                  </el-dropdown-item>
                </slot>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <span v-if="!flag" class="versionLabel">{{
            getVersionLabel(versionList[currentVersionIndex])
          }}</span>
        </span>
        <slot v-if="step === 1 && !isStreaming">
          <el-dropdown @command="handleContentCommand" trigger="click" :teleported="false" append-to="parent" data-edit-id="400">
            <el-button text type="primary" data-edit-id="312">
              操作
              <el-icon class="el-icon--right" data-edit-id="402">
                <arrow-down data-edit-id="403" />
              </el-icon>
            </el-button>

            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item command="htmlDownload">
                  存档 HTML
                </el-dropdown-item>

                <el-dropdown-item command="markdownDownload">
                  存档 Markdown
                </el-dropdown-item>

                <el-dropdown-item command="imageDownload">
                  导出图片
                </el-dropdown-item>

                <el-dropdown-item command="editContent">
                  {{ viewMode ? "查看内容" : "手动修改" }}
                </el-dropdown-item>

                <el-dropdown-item command="copyLink">
                  复制链接
                </el-dropdown-item>

                <el-dropdown-item command="readLocal">
                  本地读档
                </el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <slot v-if="viewMode">
            <el-tag class="versionIndexSpan">最近第 {{ currentVersionIndex + 1 }} 个版本
            </el-tag>
            <el-button class="startModifyBtn" text type="primary" size="small" @click="startModify">开始修改</el-button>
          </slot>
          <el-input v-else :style="{ width: flag ? '260px' : '400px' }" v-model="modifyInfo" placeholder="输入变更描述..." class="custom-input-group" clearable @keyup.enter="handleConfirm" data-edit-id="342">
            <template #append>
              <el-button :loading="modifyLoading" :disabled="modifyLoading" style="color: #fff" class="!bg-blue-500 !border-blue-500 !text-white hover:!bg-blue-600 transition-all duration-300 wizard-action-btn wizard-action-btn--primary" @click="handleConfirm">
                <span class="font-medium px-2"><i class="bi bi-magic"></i></span>
              </el-button>
            </template>
          </el-input>
        </slot>
      </span>
      <!-- 结果展示面板 -->
      <div class="wizard-result-panel tw-overflow-hidden" :style="{
          height: resultPanelHeight,
          'margin-top': '0px',
          zIndex: 0,
        }" data-edit-id="110">
        <div v-if="step === 1" data-edit-id="117" class="resultComponentArea tw-w-full tw-relative" ref="resultComponentItem"></div>
        <div v-else class="tw-flex-1 tw-w-full tw-text-center tw-py-20 tw-flex tw-flex-col tw-items-center" data-edit-id="112">
          <div class="waiting-rune tw-mb-4" data-edit-id="118">
            <!-- 外环 -->
            <div class="rune-ring rune-ring--outer" data-edit-id="276"></div>
            <!-- 内环 -->
            <div class="rune-ring rune-ring--inner" data-edit-id="277"></div>
            <!-- 中心图标 -->
            <el-icon :size="32" class="rune-icon" data-edit-id="119">
              <i class="bi bi-hourglass-split"></i>
            </el-icon>
          </div>
          <p class="waiting-tips" data-edit-id="121">{{ currentTips }}</p>
          <!-- 装饰性点 -->
          <div class="magic-dots" data-edit-id="281">
            <span data-edit-id="282"></span><span data-edit-id="283"></span><span data-edit-id="284"></span>
          </div>
        </div>
      </div>
    </div>
    <el-dialog v-model="feedbackDialog.visible" title="反馈与建议" width="500" draggable>
      <el-input type="textarea" :placeholder="feedbackDialog.placeholder" v-model="feedbackDialog.message" resize="none"></el-input>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="feedbackDialog.visible = false">取消</el-button>
          <el-button type="primary" @click="confirmSendFeedback()">
            确认
          </el-button>
        </div>
      </template>
    </el-dialog>
    <input ref="hiddenFileInput" type="file" :accept="myAccept" class="tw-hidden" @change="onFileSelected($event)" data-edit-id="109" />
  </div>
</template>

<style scoped>
  /* ============================================
   RESET
   ============================================ */
  .el-card {
    border: none;
    box-shadow: none;
  }

  /* ============================================
   DESIGN TOKENS — 学术 · 精准 · 启发
   ============================================ */
  * {
    /* 主色：活力蓝（学术蓝） */
    --academic-primary: #0066CC;
    --academic-primary-hover: #0052A3;
    --academic-primary-active: #004C99;
    --academic-primary-soft: rgba(0, 102, 204, 0.08);
    --academic-primary-ring: rgba(0, 102, 204, 0.16);

    /* 背景：冰川灰 */
    --academic-bg: #EEF3F0;
    --academic-bg-deep: #F6F9F7;

    /* 文字：深石板青 */
    --academic-text: #1e293b;
    --academic-text-secondary: #475569;
    --academic-text-tertiary: #94a3b8;

    /* 强调：琥珀金 */
    --academic-accent: #f59e0b;
    --academic-accent-soft: rgba(245, 158, 11, 0.12);

    /* 辅助 */
    --academic-border: rgba(30, 41, 59, 0.08);
    --academic-border-strong: rgba(30, 41, 59, 0.14);

    /* 玻璃面板 */
    --glass-bg: rgba(255, 255, 255, 0.72);
    --glass-shadow: 0 1px 2px rgba(30, 41, 59, 0.04),
      0 8px 24px rgba(30, 41, 59, 0.06);
    --glass-shadow-hover: 0 2px 4px rgba(30, 41, 59, 0.05),
      0 12px 32px rgba(30, 41, 59, 0.09);

    /* 圆角 */
    --radius-card: 16px;
    --radius-control: 10px;

    --font-system: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI",
      "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", Arial, sans-serif;
    --font-mono: "JetBrains Mono", "Fira Code", "SF Mono", Menlo, "Cascadia Code",
      Consolas, monospace;
  }

  /* ============================================
   全局背景 — 冰川灰 + 学术点阵纹理
   ============================================ */
  .wizard-bg {
    font-family: var(--font-system);
    color: var(--academic-text);
    background-color: var(--academic-bg);
    background-image: radial-gradient(ellipse 80% 60% at 20% 0%,
        rgba(0, 102, 204, 0.06) 0%,
        transparent 55%),
      radial-gradient(ellipse 60% 50% at 100% 100%,
        rgba(245, 158, 11, 0.04) 0%,
        transparent 55%),
      radial-gradient(circle at 1px 1px,
        rgba(30, 41, 59, 0.07) 1px,
        transparent 0);
    background-size: auto, auto, 22px 22px;
    background-position: 0 0, 0 0, 0 0;
    position: relative;
  }

  .wizard-bg::before {
    content: "";
    position: absolute;
    inset: 0;
    background-image: linear-gradient(to right,
        rgba(0, 102, 204, 0.035) 1px,
        transparent 1px),
      linear-gradient(to bottom, rgba(0, 102, 204, 0.035) 1px, transparent 1px);
    background-size: 88px 88px;
    pointer-events: none;
    z-index: 0;
  }

  /* ============================================
   导航栏
   ============================================ */
  .wizard-navbar {
    position: relative;
    z-index: 1000;
    gap: 20px;
    padding: 10px 4px 18px;
    box-sizing: border-box;
  }

  .versionIndexSpan {
    font-size: 14px;
    font-weight: 900;
  }

  .wizard-navbar::after {
    content: "";
    position: absolute;
    bottom: -1px;
    left: 0;
    right: 0;
    height: 1px;
    background: linear-gradient(90deg,
        transparent 0%,
        rgba(0, 102, 204, 0.22) 30%,
        rgba(0, 102, 204, 0.22) 70%,
        transparent 100%);
  }

  /* 右侧操作区：按钮与账号区共用同一条垂直中心线 */
  .navbar-actions {
    display: flex;
    align-items: center;
    gap: 12px;
    flex: 0 0 auto;
  }

  .navbar-action-btn {
    height: 40px;
    padding: 0 16px;
  }

  .navbar-action-btn:focus-visible {
    outline: 2px solid var(--academic-primary-ring);
    outline-offset: 2px;
  }

  .navbar-divider {
    flex: 0 0 auto;
    width: 1px;
    height: 22px;
    background: var(--academic-border-strong);
    border-radius: 1px;
  }

  /* 登录 / 用户区：容纳 loginBar 44px 的切换按钮并垂直居中 */
  .navbar-account {
    display: flex;
    align-items: center;
    justify-content: flex-end;
    flex: 0 0 auto;
    width: 135px;
    height: 44px;
  }

  /* loginBar 由 CardItem 渲染，尺寸跟随账号区 */
  .login-bar-card {
    width: 100%;
    height: 100%;
  }

  .logoInfoArea {
    flex: 1 1 auto;
    min-width: 0;
  }

  .logo-title {
    display: flex;
    align-items: center;
    gap: 12px;
    min-width: 0;
    font-family: var(--font-system);
  }

  .logo-badge {
    flex: 0 0 auto;
    width: 40px;
    height: 40px;
    border-radius: 12px;
    background: linear-gradient(135deg, var(--academic-accent) 0%, #fbbf24 100%);
    color: #fff;
    box-shadow: 0 2px 8px rgba(245, 158, 11, 0.26),
      inset 0 1px 0 rgba(255, 255, 255, 0.34);
  }

  .logo-badge-icon {
    font-size: 20px;
    font-weight: 700;
    line-height: 1;
  }

  .refined-logo {
    width: 56px;
    height: 56px;
    border-radius: 14px;
    border: 1px solid var(--academic-border);
    background: #fff;
    box-shadow: 0 1px 2px rgba(30, 41, 59, 0.06),
      0 4px 12px rgba(0, 102, 204, 0.08);
    display: inline-block;
    transition: transform 0.25s ease, box-shadow 0.25s ease;
    overflow: hidden;
  }

  .refined-logo:hover {
    transform: translateY(-1px);
    box-shadow: 0 2px 4px rgba(30, 41, 59, 0.08),
      0 8px 20px rgba(0, 102, 204, 0.14);
  }

  .logo-text {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 3px;
    flex: 1 1 auto;
    min-width: 0;
    color: var(--academic-text);
    letter-spacing: -0.01em;
    line-height: 1.3;
  }

  .upload-btn {
    background: #fff !important;
    border: 1px solid var(--academic-border-strong) !important;
    color: var(--academic-text) !important;
    border-radius: 10px !important;
    font-size: 13px !important;
    transition: all 0.18s ease;
  }

  .upload-btn:hover {
    border-color: var(--academic-primary) !important;
    color: var(--academic-primary) !important;
    box-shadow: 0 3px 10px rgba(0, 102, 204, 0.14);
    transform: translateY(-1px);
  }

  .title-text {
    font-size: 16px;
    font-weight: 600;
    line-height: 1.35;
    color: var(--academic-text);
    letter-spacing: 0.01em;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .slogn-text {
    font-size: 12px;
    font-weight: 400;
    line-height: 1.4;
    color: var(--academic-text-secondary);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .refined-user-card {
    opacity: 1 !important;
  }

  .el-dropdown-menu__item {
    font-weight: 800;
  }

  /* ============================================
   面板 — 卡片式架构（2xl 圆角 + 磨砂玻璃）
   ============================================ */
  .wizard-input-panel {
    margin-top: -5px;
    margin-bottom: 5px;
  }

  .wizard-input-panel,
  .wizard-result-panel {
    position: relative;
    background: var(--glass-bg);
    border: 1px solid var(--academic-border);
    border-radius: var(--radius-card);
    box-shadow: var(--glass-shadow);
    backdrop-filter: saturate(160%) blur(18px);
    -webkit-backdrop-filter: saturate(160%) blur(18px);
    overflow: hidden;
    transition: box-shadow 0.25s ease, border-color 0.25s ease;
    z-index: 1;
  }

  .wizard-result-panel {
    position: relative;
    width: 100%;
    border-radius: var(--radius-card);
    overflow: hidden;
    transition: box-shadow 0.25s ease, border-color 0.25s ease;
    z-index: 1;
  }

  .wizard-input-panel:hover {
    box-shadow: var(--glass-shadow-hover);
  }

  /* 面板顶部细线强调 —— 学术精准感 */
  .wizard-input-panel::before {
    content: "";
    position: absolute;
    top: 0;
    left: 24px;
    right: 24px;
    height: 2px;
    background: linear-gradient(90deg,
        transparent 0%,
        var(--academic-primary) 20%,
        var(--academic-primary) 80%,
        transparent 100%);
    opacity: 0.55;
    border-radius: 2px;
  }

  .panel-glow-strip--result {
    background: linear-gradient(90deg,
        transparent 0%,
        rgba(0, 102, 204, 0.5) 25%,
        rgba(0, 102, 204, 0.55) 55%,
        rgba(245, 158, 11, 0.35) 80%,
        transparent 100%);
    background-size: 200% 100%;
  }

  @keyframes shimmerStrip {
    0% {
      background-position: 0% 0;
    }

    100% {
      background-position: 200% 0;
    }
  }

  /* ============================================
   上传区域（保留样式）
   ============================================ */
  .upload-zone {
    width: 80px;
    height: 80px;
    border: 1.5px dashed rgba(0, 102, 204, 0.35);
    border-radius: var(--radius-control);
    background: rgba(0, 102, 204, 0.04);
    cursor: pointer;
    transition: all 0.2s cubic-bezier(0.34, 1.3, 0.64, 1);
    position: relative;
    overflow: hidden;
  }

  .upload-zone::before {
    content: "";
    position: absolute;
    inset: 0;
    background: radial-gradient(circle at 50% 0%,
        rgba(0, 102, 204, 0.1),
        transparent 70%);
    opacity: 0;
    transition: opacity 0.2s ease;
  }

  .upload-zone:hover {
    border-color: var(--academic-primary);
    border-style: solid;
    background: rgba(0, 102, 204, 0.07);
    transform: translateY(-1px);
    box-shadow: 0 4px 14px rgba(0, 102, 204, 0.16);
  }

  .upload-zone:hover::before {
    opacity: 1;
  }

  .upload-zone__icon {
    color: var(--academic-primary) !important;
    transition: transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  .upload-zone:hover .upload-zone__icon {
    transform: scale(1.15) rotate(90deg);
  }

  .upload-zone__label {
    font-size: 11px;
    margin-top: 6px;
    color: var(--academic-primary);
    font-weight: 500;
    letter-spacing: 0.01em;
    font-family: var(--font-system);
  }

  .selected-image-wrap {
    display: inline-block;
  }

  .preview-thumbnail {
    width: 80px !important;
    height: 80px !important;
    border-radius: var(--radius-control) !important;
    border: 1px solid var(--academic-border) !important;
    box-shadow: 0 2px 8px rgba(30, 41, 59, 0.08), 0 1px 2px rgba(30, 41, 59, 0.04) !important;
    transition: all 0.2s ease;
  }

  .preview-thumbnail:hover {
    border-color: var(--academic-primary) !important;
    box-shadow: 0 4px 16px rgba(0, 102, 204, 0.18),
      0 1px 4px rgba(30, 41, 59, 0.06) !important;
    transform: translateY(-1px);
  }

  .removePictureBtn {
    position: absolute;
    background: #ffffff !important;
    border: 1px solid var(--academic-border-strong) !important;
    color: #dc2626 !important;
    right: -9px;
    top: -9px;
    z-index: 3;
    display: inline-block;
    box-shadow: 0 2px 6px rgba(30, 41, 59, 0.12),
      0 0 0 0.5px rgba(255, 255, 255, 0.8) inset !important;
    transition: all 0.18s ease !important;
  }

  .removePictureBtn:hover {
    background: #fee2e2 !important;
    border-color: rgba(220, 38, 38, 0.4) !important;
    box-shadow: 0 3px 10px rgba(220, 38, 38, 0.22) !important;
    transform: scale(1.08);
  }

  .el-popper {
    z-index: 10000 !important;
  }

  .versionLabel {
    font-size: 12px;
    color: #999;
    vertical-align: middle;
  }

  .startModifyBtn {
    margin-left: 12px;
  }

  /* ============================================
   文本输入区 — 对话驱动
   ============================================ */
  .input-spell-box {
    background: #ffffff;
    border: 1px solid var(--academic-border-strong);
    border-radius: var(--radius-control);
    padding: 2px;
    transition: all 0.2s ease;
    box-shadow: 0 1px 2px rgba(30, 41, 59, 0.03) inset,
      0 1px 2px rgba(255, 255, 255, 0.8);
  }

  .input-spell-box:focus-within {
    border-color: var(--academic-primary);
    box-shadow: 0 0 0 3px var(--academic-primary-ring),
      0 1px 2px rgba(30, 41, 59, 0.03) inset;
  }

  .feature-input .el-textarea__inner {
    height: 70px;
    padding: 12px 14px;
    background: transparent !important;
    color: var(--academic-text) !important;
    box-shadow: none !important;
    font-size: 14px !important;
    font-family: var(--font-system) !important;
    line-height: 1.6 !important;
    border: none !important;
    resize: none !important;
    letter-spacing: 0 !important;
  }

  .feature-input .el-textarea__inner::placeholder {
    color: var(--academic-text-tertiary);
    font-family: var(--font-system);
  }

  /* ============================================
   示例芯片
   ============================================ */
  .example-hint {
    color: var(--academic-text-tertiary);
    font-weight: 500;
  }

  .example-chip {
    background: rgba(255, 255, 255, 0.7);
    border: 1px solid var(--academic-border);
    color: var(--academic-text-secondary);
    border-radius: 999px;
    font-family: var(--font-system);
    font-weight: 500;
    backdrop-filter: blur(8px);
    transition: all 0.18s ease;
    cursor: pointer;
  }

  .example-chip:hover {
    background: #ffffff;
    border-color: var(--academic-primary);
    color: var(--academic-primary);
    box-shadow: 0 2px 8px rgba(0, 102, 204, 0.14);
    transform: translateY(-1px);
  }

  .example-chip .chip-icon {
    filter: grayscale(40%);
    transition: filter 0.2s ease;
  }

  .example-chip:hover .chip-icon {
    filter: grayscale(0%);
  }

  /* ============================================
   生成按钮 — 学术蓝 CTA
   ============================================ */
  .cast-spell-btn {
    position: relative;
    background: var(--academic-primary) !important;
    border: 1px solid var(--academic-primary) !important;
    border-radius: var(--radius-control) !important;
    font-weight: 600 !important;
    font-size: 14px !important;
    font-family: var(--font-system) !important;
    letter-spacing: 0.01em !important;
    color: #fff !important;
    padding: 10px 18px !important;
    box-shadow: 0 2px 6px rgba(0, 102, 204, 0.32), 0 1px 2px rgba(30, 41, 59, 0.1),
      inset 0 1px 0 rgba(255, 255, 255, 0.2) !important;
    transition: all 0.2s cubic-bezier(0.34, 1.2, 0.64, 1) !important;
    overflow: hidden;
  }

  .cast-spell-btn::before {
    content: "";
    position: absolute;
    top: 0;
    left: -100%;
    width: 55%;
    height: 100%;
    background: linear-gradient(90deg,
        transparent,
        rgba(255, 255, 255, 0.18),
        transparent);
    transition: left 0.6s ease;
  }

  .cast-spell-btn:hover::before {
    left: 160%;
  }

  .cast-spell-btn:hover {
    background: var(--academic-primary-hover) !important;
    border-color: var(--academic-primary-hover) !important;
    transform: translateY(-2px) !important;
    box-shadow: 0 6px 18px rgba(0, 102, 204, 0.4),
      0 2px 5px rgba(30, 41, 59, 0.12), inset 0 1px 0 rgba(255, 255, 255, 0.22) !important;
  }

  .cast-spell-btn[active="true"] {
    background: var(--academic-primary-active) !important;
  }

  .cast-spell-btn:active {
    transform: translateY(0px) scale(0.99) !important;
    background: var(--academic-primary-active) !important;
    box-shadow: 0 1px 3px rgba(0, 102, 204, 0.3),
      inset 0 1px 2px rgba(0, 0, 0, 0.1) !important;
  }

  /* ============================================
   等待状态 — 精密坐标圆环 + 十字准星
   ============================================ */
  .waiting-rune {
    position: relative;
    width: 112px;
    height: 112px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .waiting-rune::before,
  .waiting-rune::after {
    content: "";
    position: absolute;
    background: rgba(0, 102, 204, 0.15);
  }

  .waiting-rune::before {
    width: 100%;
    height: 1px;
    top: 50%;
  }

  .waiting-rune::after {
    width: 1px;
    height: 100%;
    left: 50%;
  }

  .rune-ring {
    position: absolute;
    border-radius: 50%;
    border: 1.5px solid transparent;
  }

  .rune-ring--outer {
    inset: 0;
    border-top-color: var(--academic-primary);
    border-right-color: rgba(0, 102, 204, 0.2);
    border-bottom-color: rgba(245, 158, 11, 0.4);
    border-left-color: transparent;
    animation: spinRune 2s linear infinite;
    box-shadow: 0 0 14px rgba(0, 102, 204, 0.14);
  }

  .rune-ring--inner {
    inset: 14px;
    border-top-color: transparent;
    border-right-color: rgba(245, 158, 11, 0.7);
    border-bottom-color: transparent;
    border-left-color: rgba(0, 102, 204, 0.4);
    animation: spinRune 1.4s linear infinite reverse;
  }

  @keyframes spinRune {
    from {
      transform: rotate(0deg);
    }

    to {
      transform: rotate(360deg);
    }
  }

  .rune-icon {
    color: var(--academic-primary) !important;
    filter: drop-shadow(0 0 6px rgba(0, 102, 204, 0.45)) drop-shadow(0 0 14px rgba(0, 102, 204, 0.18)) !important;
    animation: runeBreath 2.2s ease-in-out infinite;
    z-index: 1;
  }

  @keyframes runeBreath {

    0%,
    100% {
      transform: scale(0.95);
      opacity: 0.82;
    }

    50% {
      transform: scale(1.05);
      opacity: 1;
    }
  }

  .goBackBtn {
    cursor: pointer;
    font-size: 16px;
    display: flex;
    justify-content: center;
    align-items: center;
    line-height: 32px;
    height: 32px;
    width: 30px;
    text-align: center;
    float: left;
    vertical-align: middle;
    margin-right: 10px;
  }

  .waiting-tips {
    color: var(--academic-text-secondary);
    font-size: 14px;
    font-weight: 400;
    font-family: var(--font-system);
    letter-spacing: 0.01em;
    margin: 0 0 14px;
  }

  .magic-dots {
    display: flex;
    gap: 6px;
    align-items: center;
  }

  .magic-dots span {
    display: inline-block;
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--academic-primary);
    animation: dotBounce 1.2s ease-in-out infinite;
    opacity: 0.5;
  }

  .magic-dots span:nth-child(2) {
    animation-delay: 0.18s;
    background: var(--academic-accent);
  }

  .magic-dots span:nth-child(3) {
    animation-delay: 0.36s;
  }

  @keyframes dotBounce {

    0%,
    80%,
    100% {
      transform: translateY(0);
      opacity: 0.35;
    }

    40% {
      transform: translateY(-8px);
      opacity: 1;
    }
  }

  /* ============================================
   结果区域
   ============================================ */
  .resultComponentArea {
    position: relative;
    float: left;
    display: inline-block;
    width: 100%;
    height: 100%;
  }

  /* ============================================
   操作按钮组
   ============================================ */
  .action-bar {
    border-top: 1px solid var(--academic-border);
    background: rgba(248, 250, 252, 0.6);
    gap: 8px;
  }

  .wizard-action-btn {
    background: #ffffff !important;
    border: 1px solid var(--academic-border-strong) !important;
    color: var(--academic-text) !important;
    border-radius: 10px !important;
    font-size: 13px !important;
    font-weight: 500 !important;
    font-family: var(--font-system) !important;
    letter-spacing: 0.01em !important;
    box-shadow: 0 1px 3px rgba(30, 41, 59, 0.05),
      0 0 0 0.5px rgba(255, 255, 255, 0.8) inset !important;
    transition: all 0.18s ease !important;
  }

  .wizard-action-btn:hover {
    background: #ffffff !important;
    border-color: var(--academic-primary) !important;
    color: var(--academic-primary) !important;
    transform: translateY(-1px) !important;
    box-shadow: 0 3px 10px rgba(0, 102, 204, 0.14),
      0 1px 3px rgba(30, 41, 59, 0.04) !important;
  }

  .wizard-action-btn:active {
    transform: translateY(0) scale(0.99) !important;
    box-shadow: 0 1px 2px rgba(30, 41, 59, 0.08) !important;
  }

  .wizard-action-btn--primary {
    background: var(--academic-primary-soft) !important;
    border-color: rgba(0, 102, 204, 0.3) !important;
    color: var(--academic-primary) !important;
  }

  .wizard-action-btn--primary:hover {
    background: rgba(0, 102, 204, 0.14) !important;
    border-color: rgba(0, 102, 204, 0.45) !important;
    color: var(--academic-primary-active) !important;
    box-shadow: 0 3px 12px rgba(0, 102, 204, 0.2) !important;
  }

  /* ============================================
   Loading 遮罩
   ============================================ */
  .el-loading-mask {
    background: rgba(248, 250, 252, 0.82) !important;
    backdrop-filter: saturate(160%) blur(12px) !important;
    -webkit-backdrop-filter: saturate(160%) blur(12px) !important;
    border-radius: var(--radius-card) !important;
  }

  .el-loading-spinner .el-loading-text {
    color: var(--academic-text-secondary) !important;
    font-size: 12.5px !important;
    font-weight: 500 !important;
    font-family: var(--font-system) !important;
    letter-spacing: 0.01em !important;
    margin-top: 10px !important;
  }

  .el-loading-spinner .circular {
    width: 36px !important;
    height: 36px !important;
  }

  .el-loading-spinner .path {
    stroke: var(--academic-primary) !important;
    stroke-width: 2.5 !important;
  }

  /* ============================================
   布局辅助
   ============================================ */
  .topHeaderArea {
    position: relative;
    width: 100%;
  }

  .aigcOptionArea {
    position: relative;
    display: inline-block;
  }

  .designInfoArea {
    position: relative;
    display: inline-block;
  }

  /* ============================================
   Element Plus 覆盖 — 输入框 append
   ============================================ */
  .custom-input-group .el-input__wrapper {
    border-radius: 10px 0 0 10px !important;
    background: #ffffff !important;
    box-shadow: 0 0 0 1px var(--academic-border-strong) inset !important;
    transition: box-shadow 0.2s ease !important;
  }

  .custom-input-group .el-input__wrapper:hover {
    box-shadow: 0 0 0 1px var(--academic-primary) inset !important;
  }

  .custom-input-group .el-input__wrapper.is-focus {
    box-shadow: 0 0 0 1px var(--academic-primary) inset,
      0 0 0 3px var(--academic-primary-ring) !important;
  }

  .custom-input-group .el-input__inner {
    color: var(--academic-text) !important;
    font-family: var(--font-system) !important;
    font-size: 13.5px !important;
  }

  .custom-input-group .el-input__inner::placeholder {
    color: var(--academic-text-tertiary) !important;
  }

  .custom-input-group .el-input-group__append {
    background: transparent !important;
    padding: 0 !important;
    border-radius: 0 10px 10px 0 !important;
    box-shadow: none !important;
    border: none !important;
  }

  .custom-input-group .el-input-group__append .el-button {
    border-radius: 0 10px 10px 0 !important;
    background: var(--academic-primary) !important;
    border: 1px solid var(--academic-primary) !important;
    color: #fff !important;
    font-family: var(--font-system) !important;
    font-weight: 600 !important;
    margin: 0 !important;
    height: 100% !important;
    box-shadow: 0 1px 2px rgba(0, 102, 204, 0.25),
      inset 0 1px 0 rgba(255, 255, 255, 0.18) !important;
    transition: all 0.18s ease !important;
  }

  .custom-input-group .el-input-group__append .el-button:hover {
    background: var(--academic-primary-hover) !important;
    border-color: var(--academic-primary-hover) !important;
    box-shadow: 0 3px 8px rgba(0, 102, 204, 0.35),
      inset 0 1px 0 rgba(255, 255, 255, 0.2) !important;
  }

  /* ============================================
   Element Plus 覆盖 — 下拉弹层
   ============================================ */
  .el-select-dropdown {
    background: rgba(255, 255, 255, 0.96) !important;
    border: 1px solid var(--academic-border) !important;
    border-radius: 14px !important;
    box-shadow: 0 10px 32px rgba(30, 41, 59, 0.12),
      0 2px 8px rgba(30, 41, 59, 0.06) !important;
    backdrop-filter: saturate(160%) blur(18px) !important;
    -webkit-backdrop-filter: saturate(160%) blur(18px) !important;
    overflow: hidden !important;
  }

  .el-select-dropdown__item {
    color: var(--academic-text) !important;
    font-size: 13.5px !important;
    font-family: var(--font-system) !important;
    border-radius: 8px !important;
    margin: 2px 5px !important;
    padding: 8px 12px !important;
    transition: all 0.14s ease !important;
    letter-spacing: 0.01em !important;
  }

  .el-select-dropdown__item:hover,
  .el-select-dropdown__item.hover {
    background: var(--academic-primary-soft) !important;
    color: var(--academic-primary) !important;
  }

  .el-select-dropdown__item.selected {
    background: var(--academic-primary-soft) !important;
    color: var(--academic-primary) !important;
    font-weight: 600 !important;
  }

  /* ============================================
   图片预览弹层
   ============================================ */
  .el-image__inner {
    max-height: 80vh;
  }

  .el-image-viewer__wrapper {
    background: rgba(15, 23, 42, 0.85) !important;
    backdrop-filter: blur(12px) !important;
  }

  /* ============================================
   滚动条美化
   ============================================ */
  .wizard-result-panel ::-webkit-scrollbar {
    width: 6px;
    height: 6px;
  }

  .wizard-result-panel ::-webkit-scrollbar-track {
    background: transparent;
    border-radius: 6px;
  }

  .wizard-result-panel ::-webkit-scrollbar-thumb {
    background: rgba(30, 41, 59, 0.18);
    border-radius: 6px;
    transition: background 0.2s ease;
  }

  .wizard-result-panel ::-webkit-scrollbar-thumb:hover {
    background: var(--academic-primary);
  }

  /* ============================================
   响应式微调
   ============================================ */
  @media (max-width: 640px) {

    .wizard-input-panel,
    .wizard-result-panel {
      border-radius: 16px;
    }

    .cast-spell-btn {
      font-size: 13.5px !important;
      padding: 9px 14px !important;
    }

    /* 窄屏：操作区换行，避免登录按钮被挤出可视区 */
    .wizard-navbar {
      flex-wrap: wrap;
      row-gap: 10px;
      padding: 10px 4px 14px;
    }

    .navbar-actions {
      margin-left: auto;
      gap: 8px;
    }

    .logo-title {
      gap: 10px;
    }

    .logo-badge {
      width: 36px;
      height: 36px;
      border-radius: 10px;
    }

    .logo-badge-icon {
      font-size: 18px;
    }

    .logo-text {
      font-size: 15px;
    }

    .navbar-divider {
      height: 18px;
    }

    .refined-logo {
      width: 36px;
      height: 36px;
      border-radius: 10px;
    }

    .wizard-bg {
      background-size: auto, auto, 18px 18px;
    }
  }

  /* ============================================
   工具类补充
   ============================================ */
  .tw-bg-gray-50\/50 {
    background-color: rgba(246, 249, 247, 0.6);
  }

  .tw-w-800 {
    max-width: 800px;
  }

  .tw-margin-auto {
    margin: auto;
  }

  /* Muse 风格统一：品牌蓝控件、弹窗与表单 */
  :deep(.el-button--primary) {
    --el-button-bg-color: #0066CC;
    --el-button-border-color: #0066CC;
    --el-button-hover-bg-color: #0052A3;
    --el-button-hover-border-color: #0052A3;
    --el-button-active-bg-color: #004C99;
    --el-button-active-border-color: #004C99;
    border-radius: 999px;
  }

  :deep(.el-dialog) {
    border-radius: 16px !important;
    overflow: hidden;
    box-shadow: 0 25px 50px -12px rgba(15, 23, 42, 0.18) !important;
  }

  :deep(.el-dialog__header) {
    margin-right: 0;
    padding: 20px 24px 12px;
    border-bottom: 1px solid #EEF3F0;
  }

  :deep(.el-dialog__title) {
    font-weight: 700;
    font-size: 1.125rem;
    color: #0f172a;
  }

  :deep(.el-dialog__body) {
    padding: 20px 24px;
  }

  :deep(.el-dialog__footer) {
    padding: 12px 24px 20px;
    border-top: 1px solid #EEF3F0;
  }

  :deep(.el-select__wrapper),
  :deep(.el-input__wrapper) {
    border-radius: 10px;
  }

  :deep(.el-tag) {
    --el-tag-bg-color: #f0f9ff;
    --el-tag-border-color: #BAE6FD;
    --el-tag-text-color: #0052A3;
  }

  .tw-text-blue-600,
  .tw-text-blue-500 {
    color: #0066CC !important;
  }

  .tw-bg-blue-50 {
    background-color: #F0F9FF !important;
  }

  .tw-bg-blue-500,
  .tw-bg-blue-600 {
    background-color: #0066CC !important;
  }

  .tw-border-blue-200 {
    border-color: #BAE6FD !important;
  }
</style>

<script>export default 
          {"name":"","data":function(
) {
return {
      loading: false,
      loadingText: "已收指令...",
      waitingText: "等待输入描述...",
      gettedTips: "已输入描述，待点击 “立即生成” 确认...",
      modifyInfo: "",
      modifyLoading: false,
      modifyEnabled: false,
      readingFile: false,
      isStreaming: false,
      versionList: [],
      currentVersionIndex: 0,
      step: 0,
      currentAppItem: {
									className: "ElementVueItem",
									task: {
										style: {
											overflow: "auto",
										},
										template: "data:text/plain;charset=utf-8;base64,PHRlbXBsYXRlPgogIDxkaXYgcmVmPSJyb290IiBjbGFzcz0icG9zdGVyLXZpZXdlciIgdGFiaW5kZXg9IjAiIEBrZXlkb3duPSJoYW5kbGVLZXlkb3duIj4KICAgIDxoZWFkZXIgY2xhc3M9InBvc3Rlci1oZWFkZXIiPgogICAgICA8ZGl2IGNsYXNzPSJwb3N0ZXItaGVhZGluZyI+CiAgICAgICAgPGRpdiBjbGFzcz0icG9zdGVyLWhlYWRpbmctbGluZSI+CiAgICAgICAgICA8aDMgY2xhc3M9InBvc3Rlci10aXRsZSI+e3sgYWNjb3VudERhdGEudGl0bGUgfX08L2gzPgogICAgICAgICAgPHNwYW4gY2xhc3M9InBvc3Rlci1yYXRpbyIgOmFyaWEtbGFiZWw9InJhdGlvQXJpYUxhYmVsIj57eyByYXRpb0xhYmVsIH19PC9zcGFuPgogICAgICAgIDwvZGl2PgogICAgICAgIDxwIGNsYXNzPSJwb3N0ZXItZGVzYyI+e3sgYWNjb3VudERhdGEuZGVzY3JpcHRpb24gfX08L3A+CiAgICAgIDwvZGl2PgoKICAgICAgPGRpdiBjbGFzcz0icG9zdGVyLXN0YXR1c2JhciI+CiAgICAgICAgPHNwYW4gY2xhc3M9InBvc3Rlci1zdGF0dXMiIDpjbGFzcz0ic3RhdHVzVG9uZSIgYXJpYS1saXZlPSJwb2xpdGUiPgogICAgICAgICAgPHNwYW4gY2xhc3M9InBvc3Rlci1zdGF0dXMtZG90IiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9zcGFuPgogICAgICAgICAgPHNwYW4gY2xhc3M9InBvc3Rlci1zdGF0dXMtdGV4dCI+e3sgc3RhdHVzVGV4dCB9fTwvc3Bhbj4KICAgICAgICA8L3NwYW4+CiAgICAgICAgPGJ1dHRvbiB2LWlmPSJwYWdlRXJyb3JzLmxlbmd0aCIgdHlwZT0iYnV0dG9uIiBjbGFzcz0icG9zdGVyLWVycm9yLWNoaXAiIHRpdGxlPSLmn6XnnIvpooTop4jpobXpnaLnmoTov5DooYzlvILluLgiIEBjbGljaz0ib3BlbkVycm9yRGlhbG9nIj4KICAgICAgICAgIDxzdmcgY2xhc3M9InBvc3Rlci1pY29uIiB2aWV3Qm94PSIwIDAgMjQgMjQiIGZpbGw9Im5vbmUiIHN0cm9rZT0iY3VycmVudENvbG9yIiBzdHJva2Utd2lkdGg9IjEuOCIgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIiBzdHJva2UtbGluZWpvaW49InJvdW5kIiBhcmlhLWhpZGRlbj0idHJ1ZSI+CiAgICAgICAgICAgIDxwYXRoIGQ9Ik0xMiAzLjUgMjEgMjBIM3oiIC8+CiAgICAgICAgICAgIDxwYXRoIGQ9Ik0xMiA5djUiIC8+CiAgICAgICAgICAgIDxjaXJjbGUgY3g9IjEyIiBjeT0iMTciIHI9IjAuOSIgZmlsbD0iY3VycmVudENvbG9yIiBzdHJva2U9Im5vbmUiIC8+CiAgICAgICAgICA8L3N2Zz4KICAgICAgICAgIDxzcGFuPuW8guW4uCB7eyBwYWdlRXJyb3JzLmxlbmd0aCB9fTwvc3Bhbj4KICAgICAgICA8L2J1dHRvbj4KICAgICAgPC9kaXY+CiAgICA8L2hlYWRlcj4KCiAgICA8ZGl2IGNsYXNzPSJwb3N0ZXItdG9vbGJhciI+CiAgICAgIDxkaXYgY2xhc3M9InBvc3Rlci10b29sZ3JvdXAiIHJvbGU9Imdyb3VwIiBhcmlhLWxhYmVsPSLnvKnmlL7mtbfmiqXpooTop4giPgogICAgICAgIDxidXR0b24gdHlwZT0iYnV0dG9uIiBjbGFzcz0icG9zdGVyLWljb24tYnRuIiA6ZGlzYWJsZWQ9Inpvb20gPD0gbWluWm9vbSIgdGl0bGU9Iue8qeWwj++8iOW/q+aNt+mUriAt77yJIiBhcmlhLWxhYmVsPSLnvKnlsI8iIEBjbGljaz0iem9vbU91dCI+CiAgICAgICAgICA8c3ZnIGNsYXNzPSJwb3N0ZXItaWNvbiIgdmlld0JveD0iMCAwIDI0IDI0IiBmaWxsPSJub25lIiBzdHJva2U9ImN1cnJlbnRDb2xvciIgc3Ryb2tlLXdpZHRoPSIyIiBzdHJva2UtbGluZWNhcD0icm91bmQiIGFyaWEtaGlkZGVuPSJ0cnVlIj4KICAgICAgICAgICAgPHBhdGggZD0iTTUgMTJoMTQiIC8+CiAgICAgICAgICA8L3N2Zz4KICAgICAgICA8L2J1dHRvbj4KICAgICAgICA8YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InBvc3Rlci16b29tLXZhbHVlIiB0aXRsZT0i5oGi5aSN5Li66YCC5bqU55S75biD77yI5b+r5o236ZSuIDDvvIkiIEBjbGljaz0iZml0UG9zdGVyIj4KICAgICAgICAgIHt7IHpvb21QZXJjZW50IH19PHNwYW4+JTwvc3Bhbj4KICAgICAgICA8L2J1dHRvbj4KICAgICAgICA8YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InBvc3Rlci1pY29uLWJ0biIgOmRpc2FibGVkPSJ6b29tID49IG1heFpvb20iIHRpdGxlPSLmlL7lpKfvvIjlv6vmjbfplK4gK++8iSIgYXJpYS1sYWJlbD0i5pS+5aSnIiBAY2xpY2s9Inpvb21JbiI+CiAgICAgICAgICA8c3ZnIGNsYXNzPSJwb3N0ZXItaWNvbiIgdmlld0JveD0iMCAwIDI0IDI0IiBmaWxsPSJub25lIiBzdHJva2U9ImN1cnJlbnRDb2xvciIgc3Ryb2tlLXdpZHRoPSIyIiBzdHJva2UtbGluZWNhcD0icm91bmQiIGFyaWEtaGlkZGVuPSJ0cnVlIj4KICAgICAgICAgICAgPHBhdGggZD0iTTEyIDV2MTRNNSAxMmgxNCIgLz4KICAgICAgICAgIDwvc3ZnPgogICAgICAgIDwvYnV0dG9uPgogICAgICAgIDxzcGFuIGNsYXNzPSJwb3N0ZXItdG9vbC1kaXZpZGVyIiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9zcGFuPgogICAgICAgIDxidXR0b24gdHlwZT0iYnV0dG9uIiBjbGFzcz0icG9zdGVyLXRleHQtYnRuIiA6ZGlzYWJsZWQ9IiFpc1pvb21lZCIgdGl0bGU9IumAguW6lOeUu+W4g++8iOW/q+aNt+mUriAw77yJIiBAY2xpY2s9ImZpdFBvc3RlciI+CiAgICAgICAgICDpgILlupQKICAgICAgICA8L2J1dHRvbj4KICAgICAgPC9kaXY+CgogICAgICA8ZGl2IGNsYXNzPSJwb3N0ZXItdG9vbGdyb3VwIHBvc3Rlci1leHBvcnRncm91cCIgcm9sZT0iZ3JvdXAiIGFyaWEtbGFiZWw9IuWvvOWHuuWbvueJhyI+CiAgICAgICAgPHNwYW4gY2xhc3M9InBvc3Rlci1leHBvcnQtbGFiZWwiPuWvvOWHujwvc3Bhbj4KICAgICAgICA8YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InBvc3Rlci1mb3JtYXQtYnRuIiA6ZGlzYWJsZWQ9IiFjYW5FeHBvcnQiIHRpdGxlPSLlr7zlh7rkuLrml6DmjZ8gUE5H77yM6YCC5ZCI5Y2w5Yi35LiO5LqM5qyh57yW6L6RIiBAY2xpY2s9ImV4cG9ydEltYWdlKCdwbmcnKSI+CiAgICAgICAgICA8c3BhbiB2LWlmPSJleHBvcnRCdXN5ICYmIGV4cG9ydEZvcm1hdCA9PT0gJ3BuZyciIGNsYXNzPSJwb3N0ZXItc3Bpbm5lciIgYXJpYS1oaWRkZW49InRydWUiPjwvc3Bhbj4KICAgICAgICAgIFBORwogICAgICAgIDwvYnV0dG9uPgogICAgICAgIDxidXR0b24gdHlwZT0iYnV0dG9uIiBjbGFzcz0icG9zdGVyLWZvcm1hdC1idG4gcG9zdGVyLWZvcm1hdC1idG4tLWpwZWciIDpkaXNhYmxlZD0iIWNhbkV4cG9ydCIgdGl0bGU9IuWvvOWHuuS4uumrmOi0qOmHjyBKUEVH77yM5L2T56ev5pu05bCPIiBAY2xpY2s9ImV4cG9ydEltYWdlKCdqcGVnJykiPgogICAgICAgICAgPHNwYW4gdi1pZj0iZXhwb3J0QnVzeSAmJiBleHBvcnRGb3JtYXQgPT09ICdqcGVnJyIgY2xhc3M9InBvc3Rlci1zcGlubmVyIiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9zcGFuPgogICAgICAgICAgSlBFRwogICAgICAgIDwvYnV0dG9uPgogICAgICAgIDxhIHYtaWY9Imxhc3RFeHBvcnQiIGNsYXNzPSJwb3N0ZXItZG93bmxvYWQtY2hpcCIgOmhyZWY9Imxhc3RFeHBvcnQudXJsIiA6ZG93bmxvYWQ9Imxhc3RFeHBvcnQuZmlsZW5hbWUiIDp0aXRsZT0ibGFzdEV4cG9ydFRpdGxlIj4KICAgICAgICAgIDxzdmcgY2xhc3M9InBvc3Rlci1pY29uIiB2aWV3Qm94PSIwIDAgMjQgMjQiIGZpbGw9Im5vbmUiIHN0cm9rZT0iY3VycmVudENvbG9yIiBzdHJva2Utd2lkdGg9IjEuOCIgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIiBzdHJva2UtbGluZWpvaW49InJvdW5kIiBhcmlhLWhpZGRlbj0idHJ1ZSI+CiAgICAgICAgICAgIDxwYXRoIGQ9Ik0xMiA0djExIiAvPgogICAgICAgICAgICA8cGF0aCBkPSJtNy41IDEwLjUgNC41IDQuNSA0LjUtNC41IiAvPgogICAgICAgICAgICA8cGF0aCBkPSJNNSAxOWgxNCIgLz4KICAgICAgICAgIDwvc3ZnPgogICAgICAgICAg5pyA6L+R5a+85Ye6CiAgICAgICAgPC9hPgogICAgICA8L2Rpdj4KICAgIDwvZGl2PgoKICAgIDxkaXYgcmVmPSJzdGFnZSIgY2xhc3M9InBvc3Rlci1zdGFnZSIgOmNsYXNzPSJ7ICdpcy16b29tZWQnOiBpc1pvb21lZCwgJ2lzLXBhbm5pbmcnOiBpc1Bhbm5pbmcgfSIgQHdoZWVsPSJoYW5kbGVXaGVlbCIgQHBvaW50ZXJkb3duPSJoYW5kbGVQb2ludGVyRG93biIgQHBvaW50ZXJtb3ZlPSJoYW5kbGVQb2ludGVyTW92ZSIgQHBvaW50ZXJ1cD0iaGFuZGxlUG9pbnRlckVuZCIgQHBvaW50ZXJjYW5jZWw9ImhhbmRsZVBvaW50ZXJFbmQiIEBkYmxjbGljaz0iaGFuZGxlU3RhZ2VEb3VibGVDbGljayI+CiAgICAgIDxkaXYgY2xhc3M9InBvc3Rlci1zcGFjZSIgOnN0eWxlPSJwb3N0ZXJTcGFjZVN0eWxlIj4KICAgICAgICA8ZGl2IHJlZj0icG9zdGVyRnJhbWUiIGNsYXNzPSJwb3N0ZXItZnJhbWUiIDpzdHlsZT0icG9zdGVyRnJhbWVTdHlsZSI+CiAgICAgICAgICA8aWZyYW1lIHJlZj0icHJldmlld0lmcmFtZSIgOnNyYz0iaWZyYW1lU3JjIiA6c3JjZG9jPSJpZnJhbWVTcmNkb2MiIGNsYXNzPSJwb3N0ZXItaWZyYW1lIiA6c2FuZGJveD0iaWZyYW1lU2FuZGJveCIgOmtleT0icmF0aW9MYWJlbCIgYWxsb3c9ImF1dG9wbGF5OyBmdWxsc2NyZWVuIiA6dGl0bGU9InJhdGlvTGFiZWwgKyAnIOa1t+aKpemihOiniCciIEBsb2FkPSJoYW5kbGVJZnJhbWVMb2FkIj48L2lmcmFtZT4KCiAgICAgICAgICA8IS0tIOWKoOi9vemBrue9qe+8mummluW4p+ecn+ato+e7mOWItuWQjua3oeWHuu+8jOmBv+WFjeWPqueci+WIsOepuueZveeUu+W4gyAtLT4KICAgICAgICAgIDx0cmFuc2l0aW9uIG5hbWU9InBvc3Rlci1mYWRlIj4KICAgICAgICAgICAgPGRpdiB2LWlmPSIhc2NlbmVSZWFkeSAmJiAhcGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4iIGNsYXNzPSJwb3N0ZXItbG9hZGluZyIgcm9sZT0ic3RhdHVzIiBhcmlhLWxpdmU9InBvbGl0ZSI+CiAgICAgICAgICAgICAgPGRpdiBjbGFzcz0icG9zdGVyLWxvYWRpbmctY2FyZCI+CiAgICAgICAgICAgICAgICA8c3BhbiBjbGFzcz0icG9zdGVyLXNwaW5uZXIgcG9zdGVyLXNwaW5uZXItLWxhcmdlIiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9zcGFuPgogICAgICAgICAgICAgICAgPGRpdiBjbGFzcz0icG9zdGVyLWxvYWRpbmctY29weSI+CiAgICAgICAgICAgICAgICAgIDxzdHJvbmc+e3sgbG9hZGluZ1RpdGxlIH19PC9zdHJvbmc+CiAgICAgICAgICAgICAgICAgIDxzcGFuPnt7IGxvYWRpbmdIaW50IH19PC9zcGFuPgogICAgICAgICAgICAgICAgPC9kaXY+CiAgICAgICAgICAgICAgPC9kaXY+CiAgICAgICAgICAgIDwvZGl2PgogICAgICAgICAgPC90cmFuc2l0aW9uPgoKICAgICAgICAgIDwhLS0g57yp5pS+5ZCO55Sx6L+Z5bGC6YCP5piO6YGu572p5o6l566h5ouW5Yqo5bmz56e75LiO5Y+M5Ye777yM6YG/5YWNIGlmcmFtZSDlkJ7mjonmjIfpkojkuovku7YgLS0+CiAgICAgICAgICA8ZGl2IGNsYXNzPSJwb3N0ZXItc3VyZmFjZS1zaGllbGQiIGFyaWEtaGlkZGVuPSJ0cnVlIj48L2Rpdj4KICAgICAgICA8L2Rpdj4KICAgICAgPC9kaXY+CgogICAgICA8ZGl2IHYtaWY9InNjZW5lUmVhZHkiIGNsYXNzPSJwb3N0ZXItc3RhZ2UtaGludCIgYXJpYS1oaWRkZW49InRydWUiPgogICAgICAgIDx0ZW1wbGF0ZSB2LWlmPSJpc1pvb21lZCI+CiAgICAgICAgICA8c3Bhbj7mi5bliqjlubPnp7s8L3NwYW4+CiAgICAgICAgICA8c3BhbiBjbGFzcz0icG9zdGVyLWhpbnQtZG90Ij7Ctzwvc3Bhbj4KICAgICAgICAgIDxzcGFuPuWPjOWHu+WkjeS9jTwvc3Bhbj4KICAgICAgICA8L3RlbXBsYXRlPgogICAgICAgIDx0ZW1wbGF0ZSB2LWVsc2U+CiAgICAgICAgICA8c3BhbiBjbGFzcz0icG9zdGVyLWhpbnQtcG9pbnRlciI+Q3RybCAvIOKMmCArIOa7mui9rue8qeaUvjwvc3Bhbj4KICAgICAgICAgIDxzcGFuIGNsYXNzPSJwb3N0ZXItaGludC10b3VjaCI+54K55oyJIMKxIOe8qeaUvjwvc3Bhbj4KICAgICAgICAgIDxzcGFuIGNsYXNzPSJwb3N0ZXItaGludC1kb3QiPsK3PC9zcGFuPgogICAgICAgICAgPHNwYW4+5Y+M5Ye75pS+5aSnPC9zcGFuPgogICAgICAgIDwvdGVtcGxhdGU+CiAgICAgIDwvZGl2PgogICAgPC9kaXY+CgogICAgPGZvb3RlciBjbGFzcz0icG9zdGVyLWZvb3RlciI+CiAgICAgIDxzcGFuIGNsYXNzPSJwb3N0ZXItZm9vdGVyLW1ldGEiPgogICAgICAgIDxzcGFuIGNsYXNzPSJwb3N0ZXItZm9vdGVyLWJhZGdlIj57eyByYXRpb0xhYmVsIH19PC9zcGFuPgogICAgICAgIHt7IG9yaWVudGF0aW9uTGFiZWwgfX3mtbfmiqUgwrcgUE5HIC8gSlBFRyDpq5jmuIXlr7zlh7oKICAgICAgPC9zcGFuPgogICAgICA8c3BhbiB2LWlmPSJsYXN0RXhwb3J0IiBjbGFzcz0icG9zdGVyLWZvb3Rlci1leHBvcnQiPgogICAgICAgIOW3suWvvOWHuiB7eyBsYXN0RXhwb3J0LndpZHRoIH19IMOXIHt7IGxhc3RFeHBvcnQuaGVpZ2h0IH19IMK3IHt7IGxhc3RFeHBvcnQuZm9ybWF0TGFiZWwgfX0gwrcge3sgbGFzdEV4cG9ydC5zaXplTGFiZWwgfX0KICAgICAgPC9zcGFuPgogICAgICA8c3BhbiB2LWVsc2UgY2xhc3M9InBvc3Rlci1mb290ZXItdGlwIj7nvKnmlL7kuI3kvJrlvbHlk43lr7zlh7rliIbovqjnjofvvIzlr7zlh7rlsLrlr7jkuLoge3sgZXhwb3J0V2lkdGggfX0gw5cge3sgZXhwb3J0SGVpZ2h0IH1944CCPC9zcGFuPgogICAgPC9mb290ZXI+CgogICAgPCEtLSDpooTop4jpobXpnaLlvILluLjlvLnnqpcgLS0+CiAgICA8dHJhbnNpdGlvbiBuYW1lPSJwb3N0ZXItZmFkZSI+CiAgICAgIDxkaXYgdi1pZj0iZXJyb3JEaWFsb2dWaXNpYmxlIiBjbGFzcz0icG9zdGVyLWVycm9yLW1hc2siIEBjbGljay5zZWxmPSJjbG9zZUVycm9yRGlhbG9nIj4KICAgICAgICA8ZGl2IHJlZj0iZXJyb3JEaWFsb2ciIGNsYXNzPSJwb3N0ZXItZXJyb3ItZGlhbG9nIiByb2xlPSJkaWFsb2ciIGFyaWEtbW9kYWw9InRydWUiIGFyaWEtbGFiZWxsZWRieT0icG9zdGVyLWVycm9yLXRpdGxlIiB0YWJpbmRleD0iLTEiIEBjbGljay5zdG9wPgogICAgICAgICAgPGRpdiBjbGFzcz0icG9zdGVyLWVycm9yLWhlYWRlciI+CiAgICAgICAgICAgIDxkaXYgY2xhc3M9InBvc3Rlci1lcnJvci1oZWFkaW5nIj4KICAgICAgICAgICAgICA8c3BhbiBjbGFzcz0icG9zdGVyLWVycm9yLWJhZGdlIiBhcmlhLWhpZGRlbj0idHJ1ZSI+ITwvc3Bhbj4KICAgICAgICAgICAgICA8ZGl2PgogICAgICAgICAgICAgICAgPGgzIGlkPSJwb3N0ZXItZXJyb3ItdGl0bGUiIGNsYXNzPSJwb3N0ZXItZXJyb3ItdGl0bGUiPumihOiniOmhtemdoui/kOihjOW8guW4uDwvaDM+CiAgICAgICAgICAgICAgICA8cCBjbGFzcz0icG9zdGVyLWVycm9yLXN1YnRpdGxlIj7mtbfmiqXpooTop4ggaWZyYW1lIOaNleiOt+WIsCB7eyBwYWdlRXJyb3JzLmxlbmd0aCB9fSDmnaHlvILluLjkv6Hmga88L3A+CiAgICAgICAgICAgICAgPC9kaXY+CiAgICAgICAgICAgIDwvZGl2PgogICAgICAgICAgICA8YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InBvc3Rlci1lcnJvci1jbG9zZSIgdGl0bGU9IuWFs+mXrSIgYXJpYS1sYWJlbD0i5YWz6Zet5byC5bi45by556qXIiBAY2xpY2s9ImNsb3NlRXJyb3JEaWFsb2ciPsOXPC9idXR0b24+CiAgICAgICAgICA8L2Rpdj4KCiAgICAgICAgICA8ZGl2IGNsYXNzPSJwb3N0ZXItZXJyb3ItYm9keSI+CiAgICAgICAgICAgIDxhcnRpY2xlIHYtZm9yPSJpdGVtIGluIHBhZ2VFcnJvcnMiIDprZXk9Iml0ZW0uaWQiIGNsYXNzPSJwb3N0ZXItZXJyb3ItaXRlbSI+CiAgICAgICAgICAgICAgPGRpdiBjbGFzcz0icG9zdGVyLWVycm9yLWl0ZW0taGVhZCI+CiAgICAgICAgICAgICAgICA8c3BhbiBjbGFzcz0icG9zdGVyLWVycm9yLXR5cGUiPnt7IGl0ZW0udHlwZUxhYmVsIH19PC9zcGFuPgogICAgICAgICAgICAgICAgPHNwYW4gY2xhc3M9InBvc3Rlci1lcnJvci10aW1lIj57eyBpdGVtLnRpbWVMYWJlbCB9fTwvc3Bhbj4KICAgICAgICAgICAgICA8L2Rpdj4KICAgICAgICAgICAgICA8cCBjbGFzcz0icG9zdGVyLWVycm9yLW1lc3NhZ2UiPnt7IGl0ZW0ubWVzc2FnZSB9fTwvcD4KICAgICAgICAgICAgICA8cCB2LWlmPSJpdGVtLmxvY2F0aW9uIiBjbGFzcz0icG9zdGVyLWVycm9yLWxvY2F0aW9uIj7kvY3nva7vvJp7eyBpdGVtLmxvY2F0aW9uIH19PC9wPgogICAgICAgICAgICAgIDxwcmUgdi1pZj0iaXRlbS5zdGFjayIgY2xhc3M9InBvc3Rlci1lcnJvci1zdGFjayI+e3sgaXRlbS5zdGFjayB9fTwvcHJlPgogICAgICAgICAgICA8L2FydGljbGU+CiAgICAgICAgICA8L2Rpdj4KCiAgICAgICAgICA8ZGl2IGNsYXNzPSJwb3N0ZXItZXJyb3ItZm9vdGVyIj4KICAgICAgICAgICAgPHNwYW4gdi1pZj0iY29weVN0YXRlID09PSAnY29waWVkJyIgY2xhc3M9InBvc3Rlci1lcnJvci1jb3B5LWhpbnQiPuW3suWkjeWItuWIsOWJqui0tOadvzwvc3Bhbj4KICAgICAgICAgICAgPHNwYW4gdi1lbHNlLWlmPSJjb3B5U3RhdGUgPT09ICdmYWlsZWQnIiBjbGFzcz0icG9zdGVyLWVycm9yLWNvcHktaGludCBpcy1lcnJvciI+5aSN5Yi25aSx6LSl77yM6K+35omL5Yqo6YCJ5oup5YaF5a65PC9zcGFuPgogICAgICAgICAgICA8YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InBvc3Rlci1lcnJvci1idG4iIDpkaXNhYmxlZD0iY29weVN0YXRlID09PSAnY29weWluZyciIEBjbGljaz0iY29weUVycm9ySW5mbyI+e3sgY29weVN0YXRlID09PSAnY29weWluZycgPyAn5aSN5Yi25Lit4oCmJyA6ICflpI3liLblvILluLjkv6Hmga8nIH19PC9idXR0b24+CiAgICAgICAgICAgIDxidXR0b24gdHlwZT0iYnV0dG9uIiBjbGFzcz0icG9zdGVyLWVycm9yLWJ0biBwb3N0ZXItZXJyb3ItYnRuLS1wcmltYXJ5IiBAY2xpY2s9ImNsb3NlRXJyb3JEaWFsb2ciPuWFs+mXrTwvYnV0dG9uPgogICAgICAgICAgPC9kaXY+CiAgICAgICAgPC9kaXY+CiAgICAgIDwvZGl2PgogICAgPC90cmFuc2l0aW9uPgogIDwvZGl2Pgo8L3RlbXBsYXRlPgo8c2NyaXB0PgogIGV4cG9ydCBkZWZhdWx0IHsKICAgIG5hbWU6ICdMZWFmZXJQb3N0ZXJTcGVjVmlld2VyJywKICAgIGRhdGEoKSB7CiAgICAgIHJldHVybiB7CiAgICAgICAgYWNjb3VudERhdGE6IHsKICAgICAgICAgIHRpdGxlOiAn5Yaw5pm26KeJ6YaSJywKICAgICAgICAgIGRlc2NyaXB0aW9uOiAnTGVhZmVySlMg5rW35oql6aKE6KeI77yM5pSv5oyB57yp5pS+44CB5ouW5Yqo5bmz56e75LiOIFBORyAvIEpQRUcg6auY5riF5a+85Ye644CCJywKCQkJCQkid2lkdGgiOiA5MDAsCgkJCQkJImhlaWdodCI6IDEyMDAsCiAgICAgICAgICBjb2RlOiAiPCFET0NUWVBFIGh0bWw+PGh0bWw+PGhlYWQ+PG1ldGEgY2hhcnNldD1cIlVURi04XCI+PG1ldGEgbmFtZT1cInZpZXdwb3J0XCIgY29udGVudD1cIndpZHRoPWRldmljZS13aWR0aCxpbml0aWFsLXNjYWxlPTFcIj48dGl0bGU+UG9zdGVyPC90aXRsZT48L2hlYWQ+PGJvZHk+PGRpdiBpZD1cInBvc3RlclwiIHN0eWxlPVwid2lkdGg6OTAwcHg7aGVpZ2h0OjEyMDBweDtiYWNrZ3JvdW5kOiMxMTE7Y29sb3I6I2ZmZjtkaXNwbGF5OmZsZXg7ZmxleC1kaXJlY3Rpb246Y29sdW1uO2p1c3RpZnktY29udGVudDpjZW50ZXI7YWxpZ24taXRlbXM6Y2VudGVyO2ZvbnQtZmFtaWx5OkFyaWFsLHNhbnMtc2VyaWY7dGV4dC1hbGlnbjpjZW50ZXJcIj48ZGl2IHN0eWxlPVwiZm9udC1zaXplOjI4cHg7bGV0dGVyLXNwYWNpbmc6OHB4O2NvbG9yOiNhYWFcIj5DUkVBVEUgU09NRVRISU5HPC9kaXY+PGRpdiBzdHlsZT1cImZvbnQtc2l6ZToxMDhweDtmb250LXdlaWdodDo5MDA7bGluZS1oZWlnaHQ6MS4xO21hcmdpbjo1MHB4IDBcIj7orqnngbXmhJ88YnI+5Y2z5Yi75Y+R55SfPC9kaXY+PGRpdiBzdHlsZT1cImZvbnQtc2l6ZToyNHB4O2NvbG9yOiNhYWFcIj5JREVBUyBJTlRPIFJFQUxJVFk8L2Rpdj48L2Rpdj48L2JvZHk+PC9odG1sPiIKICAgICAgICB9LAoKICAgICAgICAvKiAtLS0tLS0tLS0tIGlmcmFtZSAvIOaMh+S7pOeKtuaAgSAtLS0tLS0tLS0tICovCiAgICAgICAgaWZyYW1lUmVhZHk6IGZhbHNlLAogICAgICAgIC8qIGlmcmFtZSDliqDovb3mlrnlvI/vvJpkYXRhIFVSTO+8iOmmlumAie+8iSDihpIgQmxvYiDihpIgc3JjZG9j77yM5b6u5L+h6YeM6YCQ57qn6ZmN57qnICovCiAgICAgICAgaWZyYW1lU291cmNlTW9kZTogJ2RhdGEnLAogICAgICAgIGlmcmFtZUJsb2JVcmw6ICcnLAogICAgICAgIC8qIGlmcmFtZSDlhoXov5DooYzml7bmmK/lkKblt7LmiJDlip/miafooYzvvIznlKjkuo7liKTmlq3mmK/lkKbpnIDopoHliIfmjaLliqDovb3mlrnlvI8gKi8KICAgICAgICBicmlkZ2VBbGl2ZTogZmFsc2UsCiAgICAgICAgc291cmNlRmFsbGJhY2tGYWlsZWQ6IGZhbHNlLAoKICAgICAgICAvKiAtLS0tLS0tLS0tIOa1t+aKpeWKoOi9veeKtuaAgSAtLS0tLS0tLS0tICovCiAgICAgICAgc2NlbmVSZWFkeTogZmFsc2UsCiAgICAgICAgc2NlbmVMb2FkU2xvdzogZmFsc2UsCiAgICAgICAgc2NlbmVMb2FkVGltZWRPdXQ6IGZhbHNlLAogICAgICAgIHBhcmVudExvYWRpbmdPdmVybGF5SGlkZGVuOiBmYWxzZSwKCiAgICAgICAgLyogLS0tLS0tLS0tLSDnvKnmlL4gLyDlubPnp7sgLS0tLS0tLS0tLSAqLwogICAgICAgIHpvb206IDEsCiAgICAgICAgbWluWm9vbTogMC41LAogICAgICAgIG1heFpvb206IDMsCiAgICAgICAgc3RhZ2VXaWR0aDogMCwKICAgICAgICBzdGFnZUhlaWdodDogMCwKICAgICAgICBpc1Bhbm5pbmc6IGZhbHNlLAoKICAgICAgICAvKiAtLS0tLS0tLS0tIOWvvOWHuiAtLS0tLS0tLS0tICovCiAgICAgICAgZXhwb3J0QnVzeTogZmFsc2UsCiAgICAgICAgZXhwb3J0Rm9ybWF0OiAnJywKICAgICAgICBsYXN0RXhwb3J0OiBudWxsLAoKICAgICAgICAvKiAtLS0tLS0tLS0tIOmihOiniOmhtemdouW8guW4uCAtLS0tLS0tLS0tICovCiAgICAgICAgcGFnZUVycm9yczogW10sCiAgICAgICAgZXJyb3JEaWFsb2dWaXNpYmxlOiBmYWxzZSwKICAgICAgICBjb3B5U3RhdGU6ICcnLAogICAgICAgIHN0YXR1c01lc3NhZ2U6ICcnCiAgICAgIH07CiAgICB9LAoKICAgIGNvbXB1dGVkOiB7CiAgICAgIC8qKgogICAgICAgKiDms6jlhaXpppbluKfmo4DmtYsgLyDmiKrlm74gLyDlvILluLjkuIrmiqXov5DooYzml7blkI7nmoTlrozmlbQgSFRNTOOAggogICAgICAgKiDor6XlgLzlj6rlnKjljp/lp4vku6PnoIHlj5jljJbml7bph43nrpfvvIzml6LnlKjkuo4gZGF0YSBVUkzvvIzkuZ/nlKjkuo4gQmxvYiAvIHNyY2RvYyDpmY3nuqfjgIIKICAgICAgICovCiAgICAgIGluamVjdGVkSHRtbCgpIHsKICAgICAgICBpZiAoIXRoaXMuYWNjb3VudERhdGEuY29kZSkgcmV0dXJuICcnOwogICAgICAgIHJldHVybiB0aGlzLmluamVjdENhcHR1cmVSdW50aW1lKHRoaXMuYWNjb3VudERhdGEuY29kZSk7CiAgICAgIH0sCgogICAgICAvKioKICAgICAgICog5rOo5oSP77ya5b+F6aG75YWI5a6M5oiQ5rOo5YWl44CB5YaN6L2sIGJhc2U2NOOAggogICAgICAgKiDljp/lp4sgYWNjb3VudERhdGEuY29kZSDkuI3ljIXlkKvku7vkvZXliqDovb3mj5DnpLrmiJbmiKrlm77pgLvovpHjgIIKICAgICAgICovCiAgICAgIGNvZGVCYXNlNjRVcmwoKSB7CiAgICAgICAgaWYgKCF0aGlzLmluamVjdGVkSHRtbCkgcmV0dXJuICcnOwogICAgICAgIGNvbnN0IGJhc2U2NERhdGEgPSBidG9hKAogICAgICAgICAgZW5jb2RlVVJJQ29tcG9uZW50KHRoaXMuaW5qZWN0ZWRIdG1sKS5yZXBsYWNlKC8lKFswLTlBLUZdezJ9KS9nLCAobWF0Y2gsIHAxKSA9PgogICAgICAgICAgICBTdHJpbmcuZnJvbUNoYXJDb2RlKCcweCcgKyBwMSkKICAgICAgICAgICkKICAgICAgICApOwogICAgICAgIHJldHVybiBgZGF0YTp0ZXh0L2h0bWw7Y2hhcnNldD11dGYtODtiYXNlNjQsJHtiYXNlNjREYXRhfWA7CiAgICAgIH0sCgogICAgICBpZnJhbWVTcmMoKSB7CiAgICAgICAgaWYgKHRoaXMuaWZyYW1lU291cmNlTW9kZSA9PT0gJ2Jsb2InKSByZXR1cm4gdGhpcy5pZnJhbWVCbG9iVXJsIHx8IG51bGw7CiAgICAgICAgaWYgKHRoaXMuaWZyYW1lU291cmNlTW9kZSA9PT0gJ3NyY2RvYycpIHJldHVybiBudWxsOwogICAgICAgIHJldHVybiB0aGlzLmNvZGVCYXNlNjRVcmwgfHwgbnVsbDsKICAgICAgfSwKCiAgICAgIGlmcmFtZVNyY2RvYygpIHsKICAgICAgICByZXR1cm4gdGhpcy5pZnJhbWVTb3VyY2VNb2RlID09PSAnc3JjZG9jJyA/IHRoaXMuaW5qZWN0ZWRIdG1sIDogbnVsbDsKICAgICAgfSwKCiAgICAgIC8qKgogICAgICAgKiBCbG9iIC8gc3JjZG9jIOaooeW8j+WmguaenOS/neeVmSBhbGxvdy1zYW1lLW9yaWdpbu+8jGlmcmFtZSDkvJrnu6fmib/niLbpobXpnaLmupDvvIwKICAgICAgICog5a2Y5Zyo5a6J5YWo6aOO6Zmp77yb6L+Z5Lik56eN5YW85a655qih5byP5Y+q5byA5pS+6ISa5pys5LiO5by556qX6IO95Yqb44CCCiAgICAgICAqLwogICAgICBpZnJhbWVTYW5kYm94KCkgewogICAgICAgIHJldHVybiB0aGlzLmlmcmFtZVNvdXJjZU1vZGUgPT09ICdkYXRhJyA/CiAgICAgICAgICAnYWxsb3ctc2NyaXB0cyBhbGxvdy1tb2RhbHMgYWxsb3ctc2FtZS1vcmlnaW4nIDoKICAgICAgICAgICdhbGxvdy1zY3JpcHRzIGFsbG93LW1vZGFscyc7CiAgICAgIH0sCgogICAgICBzdGF0dXNUb25lKCkgewogICAgICAgIGlmICh0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkIHx8IHRoaXMuc2NlbmVMb2FkVGltZWRPdXQpIHJldHVybiAnaXMtZXJyb3InOwogICAgICAgIGlmICh0aGlzLmV4cG9ydEJ1c3kpIHJldHVybiAnaXMtYnVzeSc7CiAgICAgICAgaWYgKHRoaXMuc2NlbmVSZWFkeSkgcmV0dXJuICdpcy1yZWFkeSc7CiAgICAgICAgcmV0dXJuICdpcy1sb2FkaW5nJzsKICAgICAgfSwKCiAgICAgIHN0YXR1c1RleHQoKSB7CiAgICAgICAgaWYgKHRoaXMuc291cmNlRmFsbGJhY2tGYWlsZWQpIHJldHVybiAn6aKE6KeI6ISa5pys5LiN5Y+v55SoJzsKICAgICAgICBpZiAodGhpcy5leHBvcnRCdXN5KSByZXR1cm4gdGhpcy5leHBvcnRGb3JtYXQgPT09ICdqcGVnJyA/ICfmraPlnKjlr7zlh7ogSlBFR+KApicgOiAn5q2j5Zyo5a+85Ye6IFBOR+KApic7CiAgICAgICAgaWYgKCF0aGlzLmlmcmFtZVJlYWR5KSByZXR1cm4gJ+mihOiniOWKoOi9veS4reKApic7CiAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHsKICAgICAgICAgIGlmICh0aGlzLnNjZW5lTG9hZFRpbWVkT3V0KSByZXR1cm4gJ+a1t+aKpeeUu+W4g+S7jeacquWwsee7qic7CiAgICAgICAgICByZXR1cm4gdGhpcy5zY2VuZUxvYWRTbG93ID8gJ+a1t+aKpeeUu+W4g+WKoOi9vei+g+aFouKApicgOiAn5rW35oql5riy5p+T5Lit4oCmJzsKICAgICAgICB9CiAgICAgICAgcmV0dXJuIHRoaXMuc3RhdHVzTWVzc2FnZSB8fCAn5bCx57uqJzsKICAgICAgfSwKCiAgICAgIGxvYWRpbmdUaXRsZSgpIHsKICAgICAgICBpZiAodGhpcy5zb3VyY2VGYWxsYmFja0ZhaWxlZCkgcmV0dXJuICfmtY/op4jlmajml6Dms5XmiafooYzpooTop4johJrmnKwnOwogICAgICAgIGlmICh0aGlzLnNjZW5lTG9hZFRpbWVkT3V0KSByZXR1cm4gJ+a1t+aKpeeUu+W4g+S7jeacquWwsee7qic7CiAgICAgICAgaWYgKHRoaXMuc2NlbmVMb2FkU2xvdykgcmV0dXJuICfmtbfmiqXnlLvluIPliqDovb3ovoPmhaInOwogICAgICAgIHJldHVybiAn5q2j5Zyo5Yqg6L29ICcgKyB0aGlzLnJhdGlvTGFiZWwgKyAnIOa1t+aKpSc7CiAgICAgIH0sCgogICAgICBsb2FkaW5nSGludCgpIHsKICAgICAgICBpZiAodGhpcy5zb3VyY2VGYWxsYmFja0ZhaWxlZCkgewogICAgICAgICAgcmV0dXJuICflvZPliY3mtY/op4jlmajml6Dms5XmiafooYzpooTop4johJrmnKzvvJvor7flsJ3or5XlnKjns7vnu5/mtY/op4jlmajkuK3miZPlvIDvvIzmiJbmo4Dmn6XmtY/op4jlmajnmoQgQ2FudmFzIDJEIOaUr+aMgeOAgic7CiAgICAgICAgfQogICAgICAgIGlmICh0aGlzLnNjZW5lTG9hZFRpbWVkT3V0KSB7CiAgICAgICAgICByZXR1cm4gdGhpcy5wYWdlRXJyb3JzLmxlbmd0aCA/CiAgICAgICAgICAgICfliqDovb3otoXml7bvvIzor7fmiZPlvIDlj7PkuIrop5LigJzlvILluLjigJ3mn6XnnIvlhbfkvZPplJnor6/jgIInIDoKICAgICAgICAgICAgJ+WKoOi9veaXtumXtOi+g+mVv++8jOivt+ajgOafpee9kee7nOaIliBMZWFmZXJKUyBDRE4g5piv5ZCm5Y+v6K6/6Zeu44CCJzsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMuc2NlbmVMb2FkU2xvdykgcmV0dXJuICfotYTmupDovoPlpKfmiJbnvZHnu5zovoPmhaLvvIzor7fnqI3lgJnigKYnOwogICAgICAgIHJldHVybiAn5q2j5Zyo5Yid5aeL5YyWIExlYWZlckpTIENhbnZhcyDnlLvluIPigKYnOwogICAgICB9LAoKICAgICAgcG9zdGVyV2lkdGgoKSB7CiAgICAgICAgY29uc3QgdmFsdWUgPSBOdW1iZXIodGhpcy5hY2NvdW50RGF0YSAmJiB0aGlzLmFjY291bnREYXRhLndpZHRoKTsKICAgICAgICByZXR1cm4gTnVtYmVyLmlzRmluaXRlKHZhbHVlKSAmJiB2YWx1ZSA+IDAgPyB2YWx1ZSA6IDkwMDsKICAgICAgfSwKCiAgICAgIHBvc3RlckhlaWdodCgpIHsKICAgICAgICBjb25zdCB2YWx1ZSA9IE51bWJlcih0aGlzLmFjY291bnREYXRhICYmIHRoaXMuYWNjb3VudERhdGEuaGVpZ2h0KTsKICAgICAgICByZXR1cm4gTnVtYmVyLmlzRmluaXRlKHZhbHVlKSAmJiB2YWx1ZSA+IDAgPyB2YWx1ZSA6IDEyMDA7CiAgICAgIH0sCgogICAgICBwb3N0ZXJSYXRpbygpIHsKICAgICAgICBjb25zdCB3aWR0aCA9IHRoaXMucG9zdGVyV2lkdGg7CiAgICAgICAgY29uc3QgaGVpZ2h0ID0gdGhpcy5wb3N0ZXJIZWlnaHQ7CiAgICAgICAgY29uc3QgdyA9IE1hdGgucm91bmQod2lkdGgpOwogICAgICAgIGNvbnN0IGggPSBNYXRoLnJvdW5kKGhlaWdodCk7CiAgICAgICAgaWYgKHcgPiAwICYmIGggPiAwICYmIE1hdGguYWJzKHdpZHRoIC0gdykgPCAwLjAwMDEgJiYgTWF0aC5hYnMoaGVpZ2h0IC0gaCkgPCAwLjAwMDEpIHsKICAgICAgICAgIGNvbnN0IGRpdmlzb3IgPSB0aGlzLmdjZCh3LCBoKTsKICAgICAgICAgIHJldHVybiB7IHdpZHRoOiB3IC8gZGl2aXNvciwgaGVpZ2h0OiBoIC8gZGl2aXNvciB9OwogICAgICAgIH0KICAgICAgICByZXR1cm4geyB3aWR0aDogd2lkdGgsIGhlaWdodDogaGVpZ2h0IH07CiAgICAgIH0sCgogICAgICByYXRpb0xhYmVsKCkgewogICAgICAgIHJldHVybiB0aGlzLnBvc3RlclJhdGlvLndpZHRoICsgJyA6ICcgKyB0aGlzLnBvc3RlclJhdGlvLmhlaWdodDsKICAgICAgfSwKCiAgICAgIHJhdGlvQXJpYUxhYmVsKCkgewogICAgICAgIHJldHVybiAn55S75biD5q+U5L6LICcgKyB0aGlzLnBvc3RlclJhdGlvLndpZHRoICsgJyDmr5QgJyArIHRoaXMucG9zdGVyUmF0aW8uaGVpZ2h0OwogICAgICB9LAoKICAgICAgb3JpZW50YXRpb25MYWJlbCgpIHsKICAgICAgICBpZiAodGhpcy5wb3N0ZXJXaWR0aCA+IHRoaXMucG9zdGVySGVpZ2h0KSByZXR1cm4gJ+aoqueJiCc7CiAgICAgICAgaWYgKHRoaXMucG9zdGVyV2lkdGggPCB0aGlzLnBvc3RlckhlaWdodCkgcmV0dXJuICfnq5bniYgnOwogICAgICAgIHJldHVybiAn5pa55b2iJzsKICAgICAgfSwKCiAgICAgIGV4cG9ydFdpZHRoKCkgewogICAgICAgIGNvbnN0IHJhdGlvID0gdGhpcy5wb3N0ZXJSYXRpbzsKICAgICAgICByZXR1cm4gcmF0aW8ud2lkdGggPj0gcmF0aW8uaGVpZ2h0CiAgICAgICAgICA/IE1hdGgucm91bmQoMjcwMCAqIHJhdGlvLndpZHRoIC8gcmF0aW8uaGVpZ2h0KQogICAgICAgICAgOiAyNzAwOwogICAgICB9LAoKICAgICAgZXhwb3J0SGVpZ2h0KCkgewogICAgICAgIGNvbnN0IHJhdGlvID0gdGhpcy5wb3N0ZXJSYXRpbzsKICAgICAgICByZXR1cm4gcmF0aW8ud2lkdGggPj0gcmF0aW8uaGVpZ2h0CiAgICAgICAgICA/IDI3MDAKICAgICAgICAgIDogTWF0aC5yb3VuZCgyNzAwICogcmF0aW8uaGVpZ2h0IC8gcmF0aW8ud2lkdGgpOwogICAgICB9LAoKICAgICAgLyoqCiAgICAgICAqIOa1t+aKpeWcqOiInuWPsOWGheeahOKAnOmAguW6lOWwuuWvuOKAneOAgue8qeaUvuWPquaUueWPmOaYvuekuuavlOS+i++8jAogICAgICAgKiDkuI3mlLnlj5ggaWZyYW1lIOeahOW4g+WxgOWwuuWvuO+8jOWboOatpOe8qeaUvuS4jeS8muW9seWTjeWvvOWHuuWIhui+qOeOh+OAggogICAgICAgKi8KICAgICAgYmFzZUZyYW1lV2lkdGgoKSB7CiAgICAgICAgLyogc3RhZ2VXaWR0aCAvIHN0YWdlSGVpZ2h0IOaYr+iInuWPsOaJo+mZpCBwYWRkaW5nIOWQjueahOWGheWuueWMuuWwuuWvuO+8jAogICAgICAgICAqIOi/meagt+enu+WKqOerr+e8qeWwjyBwYWRkaW5nIOaXtua1t+aKpeiDveWQjOatpeaUvuWkp++8jOS4jeS8mueVmeS4i+WkmuS9meeVmeeZveOAgiAqLwogICAgICAgIGNvbnN0IGF2YWlsYWJsZVdpZHRoID0gTWF0aC5tYXgoMCwgdGhpcy5zdGFnZVdpZHRoKTsKICAgICAgICBjb25zdCBhdmFpbGFibGVIZWlnaHQgPSBNYXRoLm1heCgwLCB0aGlzLnN0YWdlSGVpZ2h0KTsKICAgICAgICBpZiAoIWF2YWlsYWJsZVdpZHRoIHx8ICFhdmFpbGFibGVIZWlnaHQpIHJldHVybiAzNjA7CiAgICAgICAgY29uc3QgcmF0aW8gPSB0aGlzLnBvc3RlclJhdGlvOwogICAgICAgIGNvbnN0IGZpdHRlZCA9IE1hdGgubWluKGF2YWlsYWJsZVdpZHRoLCBhdmFpbGFibGVIZWlnaHQgKiByYXRpby53aWR0aCAvIHJhdGlvLmhlaWdodCk7CiAgICAgICAgcmV0dXJuIE1hdGgubWF4KDE4MCwgTWF0aC5yb3VuZChmaXR0ZWQpKTsKICAgICAgfSwKCiAgICAgIGJhc2VGcmFtZUhlaWdodCgpIHsKICAgICAgICBjb25zdCByYXRpbyA9IHRoaXMucG9zdGVyUmF0aW87CiAgICAgICAgcmV0dXJuIE1hdGgucm91bmQodGhpcy5iYXNlRnJhbWVXaWR0aCAqIHJhdGlvLmhlaWdodCAvIHJhdGlvLndpZHRoKTsKICAgICAgfSwKCiAgICAgIHBvc3RlclNwYWNlU3R5bGUoKSB7CiAgICAgICAgY29uc3Qgc2NhbGUgPSB0aGlzLnpvb207CiAgICAgICAgcmV0dXJuIHsKICAgICAgICAgIHdpZHRoOiAodGhpcy5iYXNlRnJhbWVXaWR0aCAqIHNjYWxlKS50b0ZpeGVkKDIpICsgJ3B4JywKICAgICAgICAgIGhlaWdodDogKHRoaXMuYmFzZUZyYW1lSGVpZ2h0ICogc2NhbGUpLnRvRml4ZWQoMikgKyAncHgnCiAgICAgICAgfTsKICAgICAgfSwKCiAgICAgIHBvc3RlckZyYW1lU3R5bGUoKSB7CiAgICAgICAgcmV0dXJuIHsKICAgICAgICAgIHdpZHRoOiB0aGlzLmJhc2VGcmFtZVdpZHRoICsgJ3B4JywKICAgICAgICAgIGhlaWdodDogdGhpcy5iYXNlRnJhbWVIZWlnaHQgKyAncHgnLAogICAgICAgICAgdHJhbnNmb3JtOiAnc2NhbGUoJyArIHRoaXMuem9vbSArICcpJywKICAgICAgICAgIHRyYW5zZm9ybU9yaWdpbjogJzAgMCcKICAgICAgICB9OwogICAgICB9LAoKICAgICAgem9vbVBlcmNlbnQoKSB7CiAgICAgICAgcmV0dXJuIE1hdGgucm91bmQodGhpcy56b29tICogMTAwKTsKICAgICAgfSwKCiAgICAgIGlzWm9vbWVkKCkgewogICAgICAgIHJldHVybiB0aGlzLnpvb20gPiAxLjAwMTsKICAgICAgfSwKCiAgICAgIGNhbkV4cG9ydCgpIHsKICAgICAgICByZXR1cm4gdGhpcy5pZnJhbWVSZWFkeSAmJiB0aGlzLnNjZW5lUmVhZHkgJiYgIXRoaXMuZXhwb3J0QnVzeTsKICAgICAgfSwKCiAgICAgIGxhc3RFeHBvcnRUaXRsZSgpIHsKICAgICAgICBpZiAoIXRoaXMubGFzdEV4cG9ydCkgcmV0dXJuICcnOwogICAgICAgIHJldHVybiAn5pyA6L+R5a+85Ye677yaJyArIHRoaXMubGFzdEV4cG9ydC53aWR0aCArICcgw5cgJyArIHRoaXMubGFzdEV4cG9ydC5oZWlnaHQgKwogICAgICAgICAgJyAnICsgdGhpcy5sYXN0RXhwb3J0LmZvcm1hdExhYmVsICsgJ++8jOeCueWHu+mHjeaWsOS4i+i9vSc7CiAgICAgIH0KICAgIH0sCgogICAgd2F0Y2g6IHsKICAgICAgY29kZUJhc2U2NFVybCgpIHsKICAgICAgICB0aGlzLmlmcmFtZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJyc7CiAgICAgICAgdGhpcy56b29tID0gMTsKICAgICAgICB0aGlzLmlzUGFubmluZyA9IGZhbHNlOwogICAgICAgIHRoaXMubGFzdEV4cG9ydCA9IG51bGw7CiAgICAgICAgdGhpcy5yZXNldFNjZW5lTG9hZGluZygpOwogICAgICAgIHRoaXMucmVzZXRQYWdlRXJyb3JzKCk7CiAgICAgICAgdGhpcy5yZWplY3RQZW5kaW5nQ29tbWFuZHMoJ+mihOiniOW3sumHjeaWsOWKoOi9vScpOwogICAgICAgIHRoaXMuY2xlYXJTb3VyY2VGYWxsYmFja0NoZWNrKCk7CiAgICAgICAgdGhpcy5yZXZva2VJZnJhbWVCbG9iVXJsKCk7CiAgICAgICAgdGhpcy5pZnJhbWVTb3VyY2VNb2RlID0gJ2RhdGEnOwogICAgICAgIHRoaXMuaWZyYW1lQmxvYlVybCA9ICcnOwogICAgICAgIHRoaXMuYnJpZGdlQWxpdmUgPSBmYWxzZTsKICAgICAgICB0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgfSwKICAgICAgcmF0aW9MYWJlbCgpIHsKICAgICAgICB0aGlzLmlmcmFtZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5wYXJlbnRMb2FkaW5nT3ZlcmxheUhpZGRlbiA9IGZhbHNlOwogICAgICAgIHRoaXMuc3RhdHVzTWVzc2FnZSA9ICcnOwogICAgICAgIHRoaXMubGFzdEV4cG9ydCA9IG51bGw7CiAgICAgICAgdGhpcy56b29tID0gMTsKICAgICAgICB0aGlzLmJyaWRnZUFsaXZlID0gZmFsc2U7CiAgICAgICAgdGhpcy5zb3VyY2VGYWxsYmFja0ZhaWxlZCA9IGZhbHNlOwogICAgICAgIHRoaXMucmVzZXRQYWdlRXJyb3JzKCk7CiAgICAgICAgdGhpcy5yZXNldFNjZW5lTG9hZGluZygpOwogICAgICAgIHRoaXMucmVqZWN0UGVuZGluZ0NvbW1hbmRzKCfpooTop4jmr5Tkvovlt7Lmm7TmlrAnKTsKICAgICAgICB0aGlzLmNsZWFyU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICAgIHRoaXMuc2NoZWR1bGVTb3VyY2VGYWxsYmFja0NoZWNrKCk7CiAgICAgIH0KICAgIH0sCgogICAgY3JlYXRlZCgpIHsKICAgICAgLyog6Z2e5ZON5bqU5byP5YaF6YOo54q25oCBICovCiAgICAgIHRoaXMucGVuZGluZ0NvbW1hbmRzID0gT2JqZWN0LmNyZWF0ZShudWxsKTsKICAgICAgdGhpcy5jb21tYW5kU2VxID0gMDsKICAgICAgdGhpcy5zdGF0dXNUaW1lciA9IG51bGw7CiAgICAgIHRoaXMuZXJyb3JTZXEgPSAwOwogICAgICB0aGlzLnNlZW5QYWdlRXJyb3JJZHMgPSBPYmplY3QuY3JlYXRlKG51bGwpOwogICAgICB0aGlzLmNvcHlSZXNldFRpbWVyID0gbnVsbDsKICAgICAgdGhpcy5lcnJvckRpYWxvZ1N1cHByZXNzZWRVbnRpbCA9IDA7CiAgICAgIHRoaXMuc2NlbmVTbG93VGltZXIgPSBudWxsOwogICAgICB0aGlzLnNjZW5lVGltZW91dFRpbWVyID0gbnVsbDsKICAgICAgdGhpcy5zY2VuZVBhcmVudE92ZXJsYXlUaW1lciA9IG51bGw7CiAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tUaW1lciA9IG51bGw7CiAgICAgIHRoaXMuc3RhZ2VSZXNpemVPYnNlcnZlciA9IG51bGw7CiAgICAgIHRoaXMucGFuU3RhcnQgPSBudWxsOwogICAgfSwKCiAgICBtb3VudGVkKCkgewogICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcignbWVzc2FnZScsIHRoaXMuaGFuZGxlTWVzc2FnZSk7CiAgICAgIHdpbmRvdy5hZGRFdmVudExpc3RlbmVyKCdyZXNpemUnLCB0aGlzLm1lYXN1cmVTdGFnZSk7CiAgICAgIHRoaXMub2JzZXJ2ZVN0YWdlKCk7CiAgICAgIHRoaXMuJG5leHRUaWNrKHRoaXMubWVhc3VyZVN0YWdlKTsKICAgICAgdGhpcy5zY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgIH0sCgogICAgLyogVnVlIDIg5L2/55SoIGJlZm9yZURlc3Ryb3nvvJtWdWUgMyDkvb/nlKggYmVmb3JlVW5tb3VudO+8jOi/memHjOWQjOaXtuWFvOWuue+8jOWGhemDqOWBmuW5guetiSAqLwogICAgYmVmb3JlRGVzdHJveSgpIHsKICAgICAgdGhpcy50ZWFyZG93bkNhcHR1cmVCcmlkZ2UoKTsKICAgIH0sCgogICAgYmVmb3JlVW5tb3VudCgpIHsKICAgICAgdGhpcy50ZWFyZG93bkNhcHR1cmVCcmlkZ2UoKTsKICAgIH0sCgogICAgbWV0aG9kczogewogICAgICBidWlsZENhcHR1cmVSdW50aW1lU291cmNlKCkgewogICAgICAgIGZ1bmN0aW9uIGxlYWZlckNhcHR1cmVSdW50aW1lKCkgewogICAgICAgICAgJ3VzZSBzdHJpY3QnOwoKICAgICAgICAgIGlmICh3aW5kb3cuX19MRUFGRVJfQ0FQVFVSRV9JTlNUQUxMRURfXykgcmV0dXJuOwogICAgICAgICAgd2luZG93Ll9fTEVBRkVSX0NBUFRVUkVfSU5TVEFMTEVEX18gPSB0cnVlOwoKICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogMS4g5LiO54i256qX5Y+j6YCa5L+hCiAgICAgICAgICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tICovCiAgICAgICAgICBmdW5jdGlvbiBwb3N0VG9QYXJlbnQoZXZlbnQsIGlkLCBhY3Rpb24sIGRhdGEpIHsKICAgICAgICAgICAgaWYgKCF3aW5kb3cucGFyZW50IHx8IHdpbmRvdy5wYXJlbnQgPT09IHdpbmRvdykgcmV0dXJuIGZhbHNlOwogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHdpbmRvdy5wYXJlbnQucG9zdE1lc3NhZ2UoewogICAgICAgICAgICAgICAgX19sZWFmZXJDYXB0dXJlOiB0cnVlLAogICAgICAgICAgICAgICAgc291cmNlOiAnbGVhZmVyLWNhcHR1cmUnLAogICAgICAgICAgICAgICAgaWQ6IGlkIHx8IG51bGwsCiAgICAgICAgICAgICAgICBhY3Rpb246IGFjdGlvbiB8fCBudWxsLAogICAgICAgICAgICAgICAgZXZlbnQ6IGV2ZW50LAogICAgICAgICAgICAgICAgZGF0YTogZGF0YSB8fCBudWxsCiAgICAgICAgICAgICAgfSwgJyonKTsKICAgICAgICAgICAgICByZXR1cm4gdHJ1ZTsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICByZXR1cm4gZmFsc2U7CiAgICAgICAgICAgIH0KICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBwb3N0RXJyb3IoaWQsIGFjdGlvbiwgZXJyb3IsIGNvZGUpIHsKICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdlcnJvcicsIGlkLCBhY3Rpb24sIHsKICAgICAgICAgICAgICBtZXNzYWdlOiBlcnJvciAmJiBlcnJvci5tZXNzYWdlID8gZXJyb3IubWVzc2FnZSA6IFN0cmluZyhlcnJvciB8fCAn5pyq55+l6ZSZ6K+vJyksCiAgICAgICAgICAgICAgY29kZTogY29kZSB8fCAnQ0FQVFVSRV9FUlJPUicKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9CgogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiAxLjEgaWZyYW1lIOWGheWKoOi9veaPkOekugogICAgICAgICAgICog6L+Z5q61IFVJIOaYr+majyBIVE1MIOmdmeaAgeazqOWFpeeahO+8iOingSBpbmplY3RDYXB0dXJlUnVudGltZe+8ie+8jOWNs+S9v+S4muWKoeiEmuacrAogICAgICAgICAgICog5oql6ZSZ44CB5oiW5b6u5L+h6ZmQ5Yi2IHBvc3RNZXNzYWdl77yM55So5oi35Lmf6IO95ZyoIGlmcmFtZSDlhoXnnIvliLDov5vluqbkuI7lpLHotKXljp/lm6DvvIwKICAgICAgICAgICAqIOiAjOS4jeaYr+WvueedgOS4gOeJh+eZveWxj+OAgummluW4p+ecn+ato+e7mOWItuWHuuWGheWuueWQjuaJjea3oeWHuuenu+mZpOOAggogICAgICAgICAgICogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLSAqLwogICAgICAgICAgdmFyIHNjZW5lUmVhZHlOb3RpZmllZCA9IGZhbHNlOwogICAgICAgICAgdmFyIGxvYWRpbmdQcm9ibGVtUmVwb3J0ZWQgPSBmYWxzZTsKCiAgICAgICAgICBmdW5jdGlvbiBnZXRJbmplY3RlZExvYWRpbmdPdmVybGF5KCkgewogICAgICAgICAgICByZXR1cm4gZG9jdW1lbnQuZ2V0RWxlbWVudEJ5SWQoJ19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fJyk7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gc2V0SW5qZWN0ZWRMb2FkaW5nQ29udGVudCh0aXRsZSwgaGludCkgewogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHZhciB0aXRsZU5vZGUgPSBkb2N1bWVudC5nZXRFbGVtZW50QnlJZCgnX19sZWFmZXJfbG9hZGluZ190aXRsZV9fJyk7CiAgICAgICAgICAgICAgdmFyIGhpbnROb2RlID0gZG9jdW1lbnQuZ2V0RWxlbWVudEJ5SWQoJ19fbGVhZmVyX2xvYWRpbmdfaGludF9fJyk7CiAgICAgICAgICAgICAgaWYgKHRpdGxlTm9kZSAmJiB0aXRsZSkgdGl0bGVOb2RlLnRleHRDb250ZW50ID0gdGl0bGU7CiAgICAgICAgICAgICAgaWYgKGhpbnROb2RlICYmIGhpbnQpIGhpbnROb2RlLnRleHRDb250ZW50ID0gaGludDsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAvKiDlv73nlaUgKi8KICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHNob3dJbmplY3RlZExvYWRpbmdQcm9ibGVtKG1lc3NhZ2UpIHsKICAgICAgICAgICAgaWYgKHNjZW5lUmVhZHlOb3RpZmllZCkgcmV0dXJuOwogICAgICAgICAgICBsb2FkaW5nUHJvYmxlbVJlcG9ydGVkID0gdHJ1ZTsKICAgICAgICAgICAgdmFyIGhpbnQgPSBtZXNzYWdlID8gU3RyaW5nKG1lc3NhZ2UpIDogJ+ivt+ajgOafpee9kee7nOOAgUxlYWZlckpTIENETiDmmK/lkKblj6/orr/pl67vvIzku6Xlj4rmtY/op4jlmajmmK/lkKbmlK/mjIEgQ2FudmFzIDJE44CCJzsKICAgICAgICAgICAgaWYgKGhpbnQubGVuZ3RoID4gMTgwKSBoaW50ID0gaGludC5zbGljZSgwLCAxODApICsgJ+KApic7CiAgICAgICAgICAgIHNldEluamVjdGVkTG9hZGluZ0NvbnRlbnQoJ+a1t+aKpeeUu+W4g+WKoOi9vemBh+WIsOmXrumimCcsIGhpbnQpOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGhpZGVJbmplY3RlZExvYWRpbmdPdmVybGF5KCkgewogICAgICAgICAgICB2YXIgb3ZlcmxheSA9IGdldEluamVjdGVkTG9hZGluZ092ZXJsYXkoKTsKICAgICAgICAgICAgaWYgKCFvdmVybGF5KSByZXR1cm47CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgb3ZlcmxheS5zdHlsZS5vcGFjaXR5ID0gJzAnOwogICAgICAgICAgICAgIG92ZXJsYXkuc3R5bGUucG9pbnRlckV2ZW50cyA9ICdub25lJzsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAvKiDlv73nlaUgKi8KICAgICAgICAgICAgfQogICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChmdW5jdGlvbigpIHsKICAgICAgICAgICAgICBpZiAob3ZlcmxheS5wYXJlbnROb2RlKSBvdmVybGF5LnBhcmVudE5vZGUucmVtb3ZlQ2hpbGQob3ZlcmxheSk7CiAgICAgICAgICAgIH0sIDM1MCk7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gc3RhcnRJbmplY3RlZExvYWRpbmdXYXRjaCgpIHsKICAgICAgICAgICAgLyogNiDnp5Lku43ml6DpppbluKfvvJrmj5DnpLrotYTmupDovoPlpKfvvJsxMiDnp5Lku43ml6DpppbluKfvvJrnu5nlh7ogTGVhZmVySlMgLyBDYW52YXMg5Yid5aeL5YyW5aSx6LSl5oyH5byV44CCCiAgICAgICAgICAgICAqIOW3sue7j+aYjuehruaKpeWRiui/h+mXrumimO+8iOWmgiBDYW52YXMg55S75biD5LiN5Y+v55So77yJ5pe25LiN5YaN6KaG55uW5pu05YW35L2T55qE5o+Q56S644CCICovCiAgICAgICAgICAgIHdpbmRvdy5zZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGlmICghc2NlbmVSZWFkeU5vdGlmaWVkICYmICFsb2FkaW5nUHJvYmxlbVJlcG9ydGVkKSB7CiAgICAgICAgICAgICAgICBzZXRJbmplY3RlZExvYWRpbmdDb250ZW50KCfmtbfmiqXnlLvluIPliqDovb3ovoPmhaInLCAnTGVhZmVySlMg5oiW5rW35oql6LWE5rqQ5LuN5Zyo5Yqg6L2977yM6K+356iN5YCZ4oCmJyk7CiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9LCA2MDAwKTsKCiAgICAgICAgICAgIHdpbmRvdy5zZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGlmIChzY2VuZVJlYWR5Tm90aWZpZWQgfHwgbG9hZGluZ1Byb2JsZW1SZXBvcnRlZCkgcmV0dXJuOwogICAgICAgICAgICAgIHNldEluamVjdGVkTG9hZGluZ0NvbnRlbnQoCiAgICAgICAgICAgICAgICAn5rW35oql55S75biD5LuN5pyq5bCx57uqJywKICAgICAgICAgICAgICAgICflj6/og73mmK8gTGVhZmVySlMg6LWE5rqQ5Yqg6L295aSx6LSl44CB5rW35oql6ISa5pys5byC5bi45oiWIENhbnZhcyDnlLvluIPmnKrliJvlu7rjgILor7fmo4Dmn6XnvZHnu5zkuI7pobXpnaLlvILluLjkv6Hmga/jgIInCiAgICAgICAgICAgICAgKTsKICAgICAgICAgICAgfSwgMTIwMDApOwogICAgICAgICAgfQoKICAgICAgICAgIC8qIExlYWZlckpTIOS9v+eUqCBDYW52YXMgMkTvvJvkuI3miafooYwgV2ViR0wg5o6i5rWL44CCQ2FudmFzIOaYr+WQpuWPr+eUqOWPiummluW4pwogICAgICAgICAgICog5piv5ZCm5a6M5oiQ77yM55Sx5LiL5pa555qE55yf5a6e55S75biD5qOA5rWL57uf5LiA5Yik5pat44CCICovCgogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiAyLiDpgJrnlKjlt6XlhbcKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGNsYW1wTnVtYmVyKHZhbHVlLCBtaW4sIG1heCwgZmFsbGJhY2spIHsKICAgICAgICAgICAgdmFyIG51bWJlciA9IHR5cGVvZiB2YWx1ZSA9PT0gJ251bWJlcicgPyB2YWx1ZSA6IHBhcnNlRmxvYXQodmFsdWUpOwogICAgICAgICAgICBpZiAoIWlzRmluaXRlKG51bWJlcikpIHJldHVybiBmYWxsYmFjazsKICAgICAgICAgICAgcmV0dXJuIE1hdGgubWluKG1heCwgTWF0aC5tYXgobWluLCBudW1iZXIpKTsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBkYXRhVXJsQnl0ZVNpemUoZGF0YVVybCkgewogICAgICAgICAgICB2YXIgY29tbWFJbmRleCA9IGRhdGFVcmwuaW5kZXhPZignLCcpOwogICAgICAgICAgICBpZiAoY29tbWFJbmRleCA9PT0gLTEpIHJldHVybiBkYXRhVXJsLmxlbmd0aDsKICAgICAgICAgICAgdmFyIGJhc2U2NCA9IGRhdGFVcmwuc2xpY2UoY29tbWFJbmRleCArIDEpOwogICAgICAgICAgICB2YXIgcGFkZGluZyA9IDA7CiAgICAgICAgICAgIGlmIChiYXNlNjQuY2hhckF0KGJhc2U2NC5sZW5ndGggLSAxKSA9PT0gJz0nKSBwYWRkaW5nICs9IDE7CiAgICAgICAgICAgIGlmIChiYXNlNjQuY2hhckF0KGJhc2U2NC5sZW5ndGggLSAyKSA9PT0gJz0nKSBwYWRkaW5nICs9IDE7CiAgICAgICAgICAgIHJldHVybiBNYXRoLm1heCgwLCBNYXRoLmZsb29yKGJhc2U2NC5sZW5ndGggKiAzIC8gNCkgLSBwYWRkaW5nKTsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBjb2xsZWN0Q2FudmFzZXMoKSB7CiAgICAgICAgICAgIHZhciBub2RlcyA9IGRvY3VtZW50LnF1ZXJ5U2VsZWN0b3JBbGwoJ2NhbnZhcycpOwogICAgICAgICAgICB2YXIgcmVzdWx0ID0gW107CiAgICAgICAgICAgIGZvciAodmFyIGkgPSAwOyBpIDwgbm9kZXMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICB2YXIgY2FudmFzID0gbm9kZXNbaV07CiAgICAgICAgICAgICAgaWYgKCFjYW52YXMgfHwgIWNhbnZhcy53aWR0aCB8fCAhY2FudmFzLmhlaWdodCkgY29udGludWU7CiAgICAgICAgICAgICAgdmFyIHJlY3QgPSBjYW52YXMuZ2V0Qm91bmRpbmdDbGllbnRSZWN0KCk7CiAgICAgICAgICAgICAgaWYgKCFyZWN0IHx8IHJlY3Qud2lkdGggPD0gMCB8fCByZWN0LmhlaWdodCA8PSAwKSBjb250aW51ZTsKICAgICAgICAgICAgICB2YXIgc3R5bGUgPSB3aW5kb3cuZ2V0Q29tcHV0ZWRTdHlsZSA/IHdpbmRvdy5nZXRDb21wdXRlZFN0eWxlKGNhbnZhcykgOiBudWxsOwogICAgICAgICAgICAgIGlmIChzdHlsZSAmJiAoc3R5bGUuZGlzcGxheSA9PT0gJ25vbmUnIHx8IHN0eWxlLnZpc2liaWxpdHkgPT09ICdoaWRkZW4nIHx8IHBhcnNlRmxvYXQoc3R5bGUub3BhY2l0eSkgPT09IDApKSBjb250aW51ZTsKICAgICAgICAgICAgICByZXN1bHQucHVzaCh7CiAgICAgICAgICAgICAgICBjYW52YXM6IGNhbnZhcywKICAgICAgICAgICAgICAgIHJlY3Q6IHJlY3QKICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgfQogICAgICAgICAgICByZXR1cm4gcmVzdWx0OwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGRvbWluYW50Q2FudmFzKCkgewogICAgICAgICAgICB2YXIgbGlzdCA9IGNvbGxlY3RDYW52YXNlcygpOwogICAgICAgICAgICB2YXIgYmVzdCA9IG51bGw7CiAgICAgICAgICAgIGZvciAodmFyIGkgPSAwOyBpIDwgbGlzdC5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIHZhciBpdGVtID0gbGlzdFtpXTsKICAgICAgICAgICAgICB2YXIgYXJlYSA9IGl0ZW0ucmVjdC53aWR0aCAqIGl0ZW0ucmVjdC5oZWlnaHQ7CiAgICAgICAgICAgICAgaWYgKCFiZXN0IHx8IGFyZWEgPiBiZXN0LmFyZWEpIHsKICAgICAgICAgICAgICAgIGJlc3QgPSB7CiAgICAgICAgICAgICAgICAgIGNhbnZhczogaXRlbS5jYW52YXMsCiAgICAgICAgICAgICAgICAgIHJlY3Q6IGl0ZW0ucmVjdCwKICAgICAgICAgICAgICAgICAgYXJlYTogYXJlYQogICAgICAgICAgICAgICAgfTsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgIH0KICAgICAgICAgICAgcmV0dXJuIGJlc3Q7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gcGFnZUJhY2tncm91bmRDb2xvcigpIHsKICAgICAgICAgICAgdmFyIG5vZGVzID0gW2RvY3VtZW50LmJvZHksIGRvY3VtZW50LmRvY3VtZW50RWxlbWVudF07CiAgICAgICAgICAgIGZvciAodmFyIGkgPSAwOyBpIDwgbm9kZXMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICBpZiAoIW5vZGVzW2ldKSBjb250aW51ZTsKICAgICAgICAgICAgICB2YXIgY29sb3IgPSB3aW5kb3cuZ2V0Q29tcHV0ZWRTdHlsZSA/IHdpbmRvdy5nZXRDb21wdXRlZFN0eWxlKG5vZGVzW2ldKS5iYWNrZ3JvdW5kQ29sb3IgOiAnJzsKICAgICAgICAgICAgICBpZiAoY29sb3IgJiYgY29sb3IgIT09ICd0cmFuc3BhcmVudCcgJiYgY29sb3IgIT09ICdyZ2JhKDAsIDAsIDAsIDApJykgcmV0dXJuIGNvbG9yOwogICAgICAgICAgICB9CiAgICAgICAgICAgIHJldHVybiAnJzsKICAgICAgICAgIH0KCiAgICAgICAgICAvKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0KICAgICAgICAgICAqIDMuIOaIquWbvuWunueOsO+8muaKiuaJgOacieWPr+ingSBjYW52YXMg5oyJIERPTSDpobrluo/lkIjmiJDliLDkuIDlvKDnlLvluIMKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGNhcHR1cmVTY3JlZW5zaG90KHBheWxvYWQpIHsKICAgICAgICAgICAgdmFyIG9wdGlvbnMgPSBwYXlsb2FkIHx8IHt9OwogICAgICAgICAgICB2YXIgaXRlbXMgPSBjb2xsZWN0Q2FudmFzZXMoKTsKICAgICAgICAgICAgaWYgKCFpdGVtcy5sZW5ndGgpIHRocm93IG5ldyBFcnJvcign5b2T5YmN6aG16Z2i5rKh5pyJ5Y+v5oiq5Zu+55qEIGNhbnZhcyDlhYPntKAnKTsKCiAgICAgICAgICAgIHZhciBtaW5MZWZ0ID0gSW5maW5pdHk7CiAgICAgICAgICAgIHZhciBtaW5Ub3AgPSBJbmZpbml0eTsKICAgICAgICAgICAgdmFyIG1heFJpZ2h0ID0gLUluZmluaXR5OwogICAgICAgICAgICB2YXIgbWF4Qm90dG9tID0gLUluZmluaXR5OwogICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IGl0ZW1zLmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICAgICAgdmFyIHJlY3QgPSBpdGVtc1tpXS5yZWN0OwogICAgICAgICAgICAgIGlmIChyZWN0LmxlZnQgPCBtaW5MZWZ0KSBtaW5MZWZ0ID0gcmVjdC5sZWZ0OwogICAgICAgICAgICAgIGlmIChyZWN0LnRvcCA8IG1pblRvcCkgbWluVG9wID0gcmVjdC50b3A7CiAgICAgICAgICAgICAgaWYgKHJlY3QucmlnaHQgPiBtYXhSaWdodCkgbWF4UmlnaHQgPSByZWN0LnJpZ2h0OwogICAgICAgICAgICAgIGlmIChyZWN0LmJvdHRvbSA+IG1heEJvdHRvbSkgbWF4Qm90dG9tID0gcmVjdC5ib3R0b207CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIHZhciB3aWR0aCA9IG1heFJpZ2h0IC0gbWluTGVmdDsKICAgICAgICAgICAgdmFyIGhlaWdodCA9IG1heEJvdHRvbSAtIG1pblRvcDsKICAgICAgICAgICAgaWYgKCEod2lkdGggPiAwKSB8fCAhKGhlaWdodCA+IDApKSB0aHJvdyBuZXcgRXJyb3IoJ2NhbnZhcyDlsLrlr7jml6DmlYjvvIzml6Dms5XmiKrlm74nKTsKCiAgICAgICAgICAgIHZhciBkb21pbmFudCA9IGRvbWluYW50Q2FudmFzKCk7CiAgICAgICAgICAgIHZhciBuYXRpdmVTY2FsZSA9IDA7CiAgICAgICAgICAgIGlmIChkb21pbmFudCkgewogICAgICAgICAgICAgIG5hdGl2ZVNjYWxlID0gTWF0aC5tYXgoCiAgICAgICAgICAgICAgICBkb21pbmFudC5jYW52YXMud2lkdGggLyBNYXRoLm1heCgxLCBkb21pbmFudC5yZWN0LndpZHRoKSwKICAgICAgICAgICAgICAgIGRvbWluYW50LmNhbnZhcy5oZWlnaHQgLyBNYXRoLm1heCgxLCBkb21pbmFudC5yZWN0LmhlaWdodCkKICAgICAgICAgICAgICApOwogICAgICAgICAgICB9CiAgICAgICAgICAgIHZhciBkZWZhdWx0U2NhbGUgPSBNYXRoLm1pbih3aW5kb3cuZGV2aWNlUGl4ZWxSYXRpbyB8fCAxLCAyKTsKICAgICAgICAgICAgdmFyIHNjYWxlID0gb3B0aW9ucy5uYXRpdmUgJiYgbmF0aXZlU2NhbGUgPiAwCiAgICAgICAgICAgICAgPyBuYXRpdmVTY2FsZQogICAgICAgICAgICAgIDogY2xhbXBOdW1iZXIob3B0aW9ucy5zY2FsZSwgMSwgMywgZGVmYXVsdFNjYWxlKTsKICAgICAgICAgICAgdmFyIG91dHB1dCA9IGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ2NhbnZhcycpOwogICAgICAgICAgICBvdXRwdXQud2lkdGggPSBNYXRoLm1heCgxLCBNYXRoLnJvdW5kKHdpZHRoICogc2NhbGUpKTsKICAgICAgICAgICAgb3V0cHV0LmhlaWdodCA9IE1hdGgubWF4KDEsIE1hdGgucm91bmQoaGVpZ2h0ICogc2NhbGUpKTsKCiAgICAgICAgICAgIHZhciBjb250ZXh0ID0gb3V0cHV0LmdldENvbnRleHQoJzJkJyk7CiAgICAgICAgICAgIGlmICghY29udGV4dCkgdGhyb3cgbmV3IEVycm9yKCfml6Dms5XliJvlu7ogMkQg55S75biD5LiK5LiL5paHJyk7CgogICAgICAgICAgICB2YXIgYmFja2dyb3VuZCA9IG9wdGlvbnMuYmFja2dyb3VuZCB8fCBwYWdlQmFja2dyb3VuZENvbG9yKCk7CiAgICAgICAgICAgIGlmIChiYWNrZ3JvdW5kKSB7CiAgICAgICAgICAgICAgY29udGV4dC5maWxsU3R5bGUgPSBiYWNrZ3JvdW5kOwogICAgICAgICAgICAgIGNvbnRleHQuZmlsbFJlY3QoMCwgMCwgb3V0cHV0LndpZHRoLCBvdXRwdXQuaGVpZ2h0KTsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgZm9yICh2YXIgaiA9IDA7IGogPCBpdGVtcy5sZW5ndGg7IGogKz0gMSkgewogICAgICAgICAgICAgIHZhciBpdGVtID0gaXRlbXNbal07CiAgICAgICAgICAgICAgdmFyIG9mZnNldFggPSAoaXRlbS5yZWN0LmxlZnQgLSBtaW5MZWZ0KSAqIHNjYWxlOwogICAgICAgICAgICAgIHZhciBvZmZzZXRZID0gKGl0ZW0ucmVjdC50b3AgLSBtaW5Ub3ApICogc2NhbGU7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIGNvbnRleHQuZHJhd0ltYWdlKAogICAgICAgICAgICAgICAgICBpdGVtLmNhbnZhcywKICAgICAgICAgICAgICAgICAgb2Zmc2V0WCwKICAgICAgICAgICAgICAgICAgb2Zmc2V0WSwKICAgICAgICAgICAgICAgICAgaXRlbS5yZWN0LndpZHRoICogc2NhbGUsCiAgICAgICAgICAgICAgICAgIGl0ZW0ucmVjdC5oZWlnaHQgKiBzY2FsZQogICAgICAgICAgICAgICAgKTsKICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgdGhyb3cgbmV3IEVycm9yKCfor7vlj5YgY2FudmFzIOWDj+e0oOWksei0pe+8micgKyAoZXJyb3IgJiYgZXJyb3IubWVzc2FnZSA/IGVycm9yLm1lc3NhZ2UgOiBlcnJvcikpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQoKICAgICAgICAgICAgdmFyIG1pbWVUeXBlID0gb3B0aW9ucy5taW1lVHlwZSA9PT0gJ2ltYWdlL2pwZWcnID8gJ2ltYWdlL2pwZWcnIDogJ2ltYWdlL3BuZyc7CiAgICAgICAgICAgIHZhciBxdWFsaXR5ID0gY2xhbXBOdW1iZXIob3B0aW9ucy5xdWFsaXR5LCAwLjEsIDEsIDAuOTUpOwogICAgICAgICAgICB2YXIgZGF0YVVybCA9IG91dHB1dC50b0RhdGFVUkwobWltZVR5cGUsIHF1YWxpdHkpOwoKICAgICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAgICBkYXRhVXJsOiBkYXRhVXJsLAogICAgICAgICAgICAgIG1pbWVUeXBlOiBtaW1lVHlwZSwKICAgICAgICAgICAgICB3aWR0aDogb3V0cHV0LndpZHRoLAogICAgICAgICAgICAgIGhlaWdodDogb3V0cHV0LmhlaWdodCwKICAgICAgICAgICAgICBzaXplOiBkYXRhVXJsQnl0ZVNpemUoZGF0YVVybCkKICAgICAgICAgICAgfTsKICAgICAgICAgIH0KICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogNC4gaWZyYW1lIOmhtemdouW8guW4uOaNleiOtwogICAgICAgICAgICog55uR5ZCs6ISa5pys6ZSZ6K+v44CB5pyq5aSE55CGIFByb21pc2XjgIHotYTmupDliqDovb3lpLHotKXkuI4gQ2FudmFzIOeUu+W4g+S4iuS4i+aWh+S4ouWkse+8jAogICAgICAgICAgICog6YCa6L+HIHBvc3RNZXNzYWdlIOS4iuaKpee7mee7hOS7tu+8m+WQjOaXtue8k+WtmOacgOi/kSA1MCDmnaHkvpvnu4Tku7booaXmi4njgIIKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIHZhciBwYWdlRXJyb3JTZXEgPSAwOwogICAgICAgICAgdmFyIHBhZ2VFcnJvckhpc3RvcnkgPSBbXTsKICAgICAgICAgIHZhciBwYWdlRXJyb3JSZWNlbnQgPSB7fTsKICAgICAgICAgIHZhciBwYWdlVG9rZW4gPSAncC0nICsgRGF0ZS5ub3coKS50b1N0cmluZygzNikgKyAnLScgKyBNYXRoLnJhbmRvbSgpLnRvU3RyaW5nKDM2KS5zbGljZSgyLCA4KTsKCiAgICAgICAgICBmdW5jdGlvbiBzYW5pdGl6ZUVycm9yVGV4dCh2YWx1ZSwgbWF4TGVuZ3RoKSB7CiAgICAgICAgICAgIHZhciB0ZXh0ID0gdmFsdWUgPT09IG51bGwgfHwgdmFsdWUgPT09IHVuZGVmaW5lZCA/ICcnIDogU3RyaW5nKHZhbHVlKTsKICAgICAgICAgICAgdGV4dCA9IHRleHQucmVwbGFjZSgvZGF0YTpbXlxzIicpXSsvZywgZnVuY3Rpb24obWF0Y2gpIHsKICAgICAgICAgICAgICByZXR1cm4gbWF0Y2gubGVuZ3RoID4gNDggPyBtYXRjaC5zbGljZSgwLCAzNikgKyAn4oCmJyA6IG1hdGNoOwogICAgICAgICAgICB9KTsKICAgICAgICAgICAgdmFyIGxpbWl0ID0gbWF4TGVuZ3RoIHx8IDIwMDA7CiAgICAgICAgICAgIGlmICh0ZXh0Lmxlbmd0aCA+IGxpbWl0KSB0ZXh0ID0gdGV4dC5zbGljZSgwLCBsaW1pdCkgKyAn4oCmJzsKICAgICAgICAgICAgcmV0dXJuIHRleHQ7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gc2FuaXRpemVFcnJvclNvdXJjZSh2YWx1ZSkgewogICAgICAgICAgICB2YXIgdGV4dCA9IHNhbml0aXplRXJyb3JUZXh0KHZhbHVlLCA4MDApOwogICAgICAgICAgICBpZiAoL15kYXRhOi9pLnRlc3QodGV4dCkpIHJldHVybiAnZGF0YTp0ZXh0L2h0bWzvvIhpZnJhbWUg5YaF6IGU6ISa5pys77yJJzsKICAgICAgICAgICAgcmV0dXJuIHRleHQ7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gcmVjb3JkUGFnZUVycm9yKHR5cGUsIGRldGFpbHMpIHsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICB2YXIgaW5mbyA9IGRldGFpbHMgfHwge307CiAgICAgICAgICAgICAgdmFyIG1lc3NhZ2UgPSBzYW5pdGl6ZUVycm9yVGV4dChpbmZvLm1lc3NhZ2UsIDIwMDApIHx8ICfmnKrnn6XlvILluLgnOwogICAgICAgICAgICAgIHZhciBzb3VyY2UgPSBzYW5pdGl6ZUVycm9yU291cmNlKGluZm8uc291cmNlKTsKICAgICAgICAgICAgICB2YXIgbGluZSA9IE51bWJlcihpbmZvLmxpbmUpIHx8IDA7CiAgICAgICAgICAgICAgdmFyIGNvbHVtbiA9IE51bWJlcihpbmZvLmNvbHVtbikgfHwgMDsKICAgICAgICAgICAgICB2YXIgc3RhY2sgPSBzYW5pdGl6ZUVycm9yVGV4dChpbmZvLnN0YWNrLCA2MDAwKTsKICAgICAgICAgICAgICB2YXIgbm93ID0gRGF0ZS5ub3coKTsKCiAgICAgICAgICAgICAgdmFyIGRlZHVwZUtleSA9IHR5cGUgKyAnfCcgKyBtZXNzYWdlICsgJ3wnICsgc291cmNlICsgJ3wnICsgbGluZSArICd8JyArIGNvbHVtbjsKICAgICAgICAgICAgICB2YXIgbGFzdEF0ID0gcGFnZUVycm9yUmVjZW50W2RlZHVwZUtleV0gfHwgMDsKICAgICAgICAgICAgICBpZiAobm93IC0gbGFzdEF0IDwgMjAwMCkgcmV0dXJuOwogICAgICAgICAgICAgIHBhZ2VFcnJvclJlY2VudFtkZWR1cGVLZXldID0gbm93OwoKICAgICAgICAgICAgICAvKiDlrprmnJ/muIXnkIbov4fmnJ/nmoTljrvph43orrDlvZUgKi8KICAgICAgICAgICAgICB2YXIgcmVjZW50S2V5cyA9IE9iamVjdC5rZXlzKHBhZ2VFcnJvclJlY2VudCk7CiAgICAgICAgICAgICAgaWYgKHJlY2VudEtleXMubGVuZ3RoID4gMTAwKSB7CiAgICAgICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IHJlY2VudEtleXMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICAgICAgaWYgKG5vdyAtIHBhZ2VFcnJvclJlY2VudFtyZWNlbnRLZXlzW2ldXSA+IDEwMDAwKSBkZWxldGUgcGFnZUVycm9yUmVjZW50W3JlY2VudEtleXNbaV1dOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgIH0KCiAgICAgICAgICAgICAgaWYgKHBhZ2VFcnJvckhpc3RvcnkubGVuZ3RoID49IDUwKSByZXR1cm47CgogICAgICAgICAgICAgIHBhZ2VFcnJvclNlcSArPSAxOwogICAgICAgICAgICAgIHZhciBwYXlsb2FkID0gewogICAgICAgICAgICAgICAgcGFnZUVycm9ySWQ6IHBhZ2VFcnJvclNlcSwKICAgICAgICAgICAgICAgIHBhZ2VUb2tlbjogcGFnZVRva2VuLAogICAgICAgICAgICAgICAgdHlwZTogdHlwZSwKICAgICAgICAgICAgICAgIG1lc3NhZ2U6IG1lc3NhZ2UsCiAgICAgICAgICAgICAgICBzb3VyY2U6IHNvdXJjZSwKICAgICAgICAgICAgICAgIGxpbmU6IGxpbmUsCiAgICAgICAgICAgICAgICBjb2x1bW46IGNvbHVtbiwKICAgICAgICAgICAgICAgIHN0YWNrOiBzdGFjaywKICAgICAgICAgICAgICAgIHRpbWVzdGFtcDogbm93CiAgICAgICAgICAgICAgfTsKCiAgICAgICAgICAgICAgcGFnZUVycm9ySGlzdG9yeS5wdXNoKHBheWxvYWQpOwogICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgncGFnZS1lcnJvcicsIG51bGwsICdwYWdlRXJyb3InLCBwYXlsb2FkKTsKICAgICAgICAgICAgfSBjYXRjaCAoaWdub3JlKSB7CiAgICAgICAgICAgICAgLyog5byC5bi45o2V6I635pys6Lqr5LiN6IO95b2x5ZON6aG16Z2iICovCiAgICAgICAgICAgIH0KICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBpbnN0YWxsUGFnZUVycm9ySGFuZGxlcnMoKSB7CiAgICAgICAgICAgIHdpbmRvdy5hZGRFdmVudExpc3RlbmVyKCdlcnJvcicsIGZ1bmN0aW9uKGV2ZW50KSB7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIHZhciB0YXJnZXQgPSBldmVudCAmJiBldmVudC50YXJnZXQ7CiAgICAgICAgICAgICAgICAvKiDmjZXojrfpmLbmrrXlj6/ku6XlkIzml7bmi7/liLDotYTmupDnsbvplJnor6/vvIhzY3JpcHQvbGluay9pbWcvYXVkaW8vdmlkZW8g562J77yJICovCiAgICAgICAgICAgICAgICBpZiAodGFyZ2V0ICYmIHRhcmdldCAhPT0gd2luZG93ICYmIHRhcmdldCAhPT0gZG9jdW1lbnQgJiYgdGFyZ2V0LnRhZ05hbWUpIHsKICAgICAgICAgICAgICAgICAgdmFyIHRhZ05hbWUgPSBTdHJpbmcodGFyZ2V0LnRhZ05hbWUpLnRvTG93ZXJDYXNlKCk7CiAgICAgICAgICAgICAgICAgIHZhciByZXNvdXJjZVVybCA9IHRhcmdldC5jdXJyZW50U3JjIHx8IHRhcmdldC5zcmMgfHwgdGFyZ2V0LmhyZWYgfHwgJyc7CiAgICAgICAgICAgICAgICAgIHJlY29yZFBhZ2VFcnJvcigncmVzb3VyY2UnLCB7CiAgICAgICAgICAgICAgICAgICAgbWVzc2FnZTogJ+i1hOa6kOWKoOi9veWksei0pe+8mjwnICsgdGFnTmFtZSArICc+ICcgKyBzYW5pdGl6ZUVycm9yVGV4dChyZXNvdXJjZVVybCwgNTAwKSwKICAgICAgICAgICAgICAgICAgICBzb3VyY2U6IHJlc291cmNlVXJsCiAgICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgICAgICByZXR1cm47CiAgICAgICAgICAgICAgICB9CgogICAgICAgICAgICAgICAgdmFyIGVycm9yID0gZXZlbnQgJiYgZXZlbnQuZXJyb3I7CiAgICAgICAgICAgICAgICByZWNvcmRQYWdlRXJyb3IoJ2Vycm9yJywgewogICAgICAgICAgICAgICAgICBtZXNzYWdlOiAoZXZlbnQgJiYgZXZlbnQubWVzc2FnZSkgfHwgKGVycm9yICYmIGVycm9yLm1lc3NhZ2UpIHx8ICfmnKrnn6XohJrmnKzplJnor68nLAogICAgICAgICAgICAgICAgICBzb3VyY2U6IChldmVudCAmJiBldmVudC5maWxlbmFtZSkgfHwgJycsCiAgICAgICAgICAgICAgICAgIGxpbmU6IChldmVudCAmJiBldmVudC5saW5lbm8pIHx8IDAsCiAgICAgICAgICAgICAgICAgIGNvbHVtbjogKGV2ZW50ICYmIGV2ZW50LmNvbG5vKSB8fCAwLAogICAgICAgICAgICAgICAgICBzdGFjazogZXJyb3IgJiYgZXJyb3Iuc3RhY2sgPyBlcnJvci5zdGFjayA6ICcnCiAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICB9IGNhdGNoIChpZ25vcmUpIHsKICAgICAgICAgICAgICAgIC8qIOW/veeVpSAqLwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfSwgdHJ1ZSk7CgogICAgICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigndW5oYW5kbGVkcmVqZWN0aW9uJywgZnVuY3Rpb24oZXZlbnQpIHsKICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgdmFyIHJlYXNvbiA9IGV2ZW50ICYmIGV2ZW50LnJlYXNvbjsKICAgICAgICAgICAgICAgIHZhciBtZXNzYWdlID0gcmVhc29uICYmIHJlYXNvbi5tZXNzYWdlID8KICAgICAgICAgICAgICAgICAgcmVhc29uLm1lc3NhZ2UgOgogICAgICAgICAgICAgICAgICBTdHJpbmcocmVhc29uID09PSB1bmRlZmluZWQgPyAn56m655qEIFByb21pc2Ug5ouS57udJyA6IHJlYXNvbik7CiAgICAgICAgICAgICAgICByZWNvcmRQYWdlRXJyb3IoJ3Byb21pc2UnLCB7CiAgICAgICAgICAgICAgICAgIG1lc3NhZ2U6IG1lc3NhZ2UsCiAgICAgICAgICAgICAgICAgIHN0YWNrOiByZWFzb24gJiYgcmVhc29uLnN0YWNrID8gcmVhc29uLnN0YWNrIDogJycKICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgIH0gY2F0Y2ggKGlnbm9yZSkgewogICAgICAgICAgICAgICAgLyog5b+955WlICovCiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9KTsKCgogICAgICAgICAgfQogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiA1LiBMZWFmZXJKUyDpppbluKflsLHnu6rmo4DmtYvvvIhzY2VuZS1yZWFkee+8iQogICAgICAgICAgICog5LiN5L6d6LWWIExlYWZlciDlhoXpg6jnp4HmnInlr7nosaHmiJYgTGVhZmVySlMg5riy5p+T5ZmoIEhvb2vjgILpgJrov4cgRE9NIOS4reecn+WungogICAgICAgICAgICogY2FudmFzIOeahOWwuuWvuOOAgeWPr+ingeaAp+WPiiBDYW52YXMgMkQg5YOP57Sg5YaF5a655Yik5pat6aaW5bin77yM5YW85a65IExlYWZlckpTCiAgICAgICAgICAgKiAyLngg55qEIFVNRCAvIEVTTSDmnoTlu7rmlrnlvI/jgILnqbrnmb3nlLvluIPkuI3kvJrooqvor6/liKTkuLrlt7LlsLHnu6rjgIIKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGNhbnZhc0hhc1BhaW50ZWQoY2FudmFzKSB7CiAgICAgICAgICAgIGlmICghY2FudmFzIHx8ICFjYW52YXMud2lkdGggfHwgIWNhbnZhcy5oZWlnaHQpIHJldHVybiBmYWxzZTsKICAgICAgICAgICAgdmFyIHJlY3QgPSBjYW52YXMuZ2V0Qm91bmRpbmdDbGllbnRSZWN0ID8gY2FudmFzLmdldEJvdW5kaW5nQ2xpZW50UmVjdCgpIDogbnVsbDsKICAgICAgICAgICAgaWYgKHJlY3QgJiYgKHJlY3Qud2lkdGggPD0gMCB8fCByZWN0LmhlaWdodCA8PSAwKSkgcmV0dXJuIGZhbHNlOwogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHZhciBjb250ZXh0ID0gY2FudmFzLmdldENvbnRleHQgJiYgY2FudmFzLmdldENvbnRleHQoJzJkJyk7CiAgICAgICAgICAgICAgaWYgKCFjb250ZXh0IHx8IHR5cGVvZiBjb250ZXh0LmdldEltYWdlRGF0YSAhPT0gJ2Z1bmN0aW9uJykgewogICAgICAgICAgICAgICAgLyog5peg5rOV6K+75Y+W5YOP57Sg5pe277yM5Y+q5Lul5bey5Yib5bu65LiU5Y+v6KeB55qE55S75biD5L2c5Li66ZmN57qn5p2h5Lu244CCICovCiAgICAgICAgICAgICAgICByZXR1cm4gISEocmVjdCAmJiByZWN0LndpZHRoID4gMCAmJiByZWN0LmhlaWdodCA+IDApOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgICB2YXIgcG9pbnRzID0gWwogICAgICAgICAgICAgICAgW01hdGguZmxvb3IoY2FudmFzLndpZHRoICogMC41KSwgTWF0aC5mbG9vcihjYW52YXMuaGVpZ2h0ICogMC41KV0sCiAgICAgICAgICAgICAgICBbTWF0aC5mbG9vcihjYW52YXMud2lkdGggKiAwLjI1KSwgTWF0aC5mbG9vcihjYW52YXMuaGVpZ2h0ICogMC4yNSldLAogICAgICAgICAgICAgICAgW01hdGguZmxvb3IoY2FudmFzLndpZHRoICogMC43NSksIE1hdGguZmxvb3IoY2FudmFzLmhlaWdodCAqIDAuNzUpXQogICAgICAgICAgICAgIF07CiAgICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBwb2ludHMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICAgIHZhciB4ID0gTWF0aC5tYXgoMCwgTWF0aC5taW4oY2FudmFzLndpZHRoIC0gMSwgcG9pbnRzW2ldWzBdKSk7CiAgICAgICAgICAgICAgICB2YXIgeSA9IE1hdGgubWF4KDAsIE1hdGgubWluKGNhbnZhcy5oZWlnaHQgLSAxLCBwb2ludHNbaV1bMV0pKTsKICAgICAgICAgICAgICAgIHZhciBwaXhlbCA9IGNvbnRleHQuZ2V0SW1hZ2VEYXRhKHgsIHksIDEsIDEpLmRhdGE7CiAgICAgICAgICAgICAgICBpZiAocGl4ZWxbM10gPiAwKSByZXR1cm4gdHJ1ZTsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgLyog5pyJ5Lqb5rW35oql6IOM5pmv6YCP5piO77yM5oq95qC354K55Y+v6IO95oGw5aW96YCP5piO77yb55S75biD5bey5a6M5oiQ5biD5bGA5pe277yMCiAgICAgICAgICAgICAgICog5YaN6YCa6L+H5aSa5Liq5Yy65Z+f55qE5L2O5YiG6L6o546H6YeH5qC35qOA5p+l5piv5ZCm5a2Y5Zyo6Z2e6YCP5piO5YOP57Sg44CCICovCiAgICAgICAgICAgICAgdmFyIHNhbXBsZVcgPSBNYXRoLm1pbigyNCwgY2FudmFzLndpZHRoKTsKICAgICAgICAgICAgICB2YXIgc2FtcGxlSCA9IE1hdGgubWluKDI0LCBjYW52YXMuaGVpZ2h0KTsKICAgICAgICAgICAgICB2YXIgc2FtcGxlID0gY29udGV4dC5nZXRJbWFnZURhdGEoMCwgMCwgc2FtcGxlVywgc2FtcGxlSCkuZGF0YTsKICAgICAgICAgICAgICBmb3IgKHZhciBwID0gMzsgcCA8IHNhbXBsZS5sZW5ndGg7IHAgKz0gNCkgewogICAgICAgICAgICAgICAgaWYgKHNhbXBsZVtwXSA+IDApIHJldHVybiB0cnVlOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgICByZXR1cm4gZmFsc2U7CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgLyog5YOP57Sg6K+75Y+W5Y+X6ZmQ5pe25LiN6Zi75aGe6aKE6KeI77ya55S75biD5a2Y5Zyo5LiU5Y+v6KeB5Y2z5Y+v6ZmN57qn5bCx57uq44CCICovCiAgICAgICAgICAgICAgcmV0dXJuICEhKHJlY3QgJiYgcmVjdC53aWR0aCA+IDAgJiYgcmVjdC5oZWlnaHQgPiAwKTsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIG5vdGlmeVNjZW5lUmVhZHlJZk5lZWRlZCgpIHsKICAgICAgICAgICAgaWYgKHNjZW5lUmVhZHlOb3RpZmllZCkgcmV0dXJuOwogICAgICAgICAgICB2YXIgY2FudmFzZXMgPSBkb2N1bWVudC5xdWVyeVNlbGVjdG9yQWxsKCdjYW52YXMnKTsKICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBjYW52YXNlcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIHZhciBjYW52YXMgPSBjYW52YXNlc1tpXTsKICAgICAgICAgICAgICBpZiAoIWNhbnZhc0hhc1BhaW50ZWQoY2FudmFzKSkgY29udGludWU7CiAgICAgICAgICAgICAgc2NlbmVSZWFkeU5vdGlmaWVkID0gdHJ1ZTsKICAgICAgICAgICAgICBoaWRlSW5qZWN0ZWRMb2FkaW5nT3ZlcmxheSgpOwogICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnc2NlbmUtcmVhZHknLCBudWxsLCAnc2NlbmVSZWFkeScsIHsKICAgICAgICAgICAgICAgIHJlYWR5OiB0cnVlLAogICAgICAgICAgICAgICAgZW5naW5lOiAnTGVhZmVySlMnLAogICAgICAgICAgICAgICAgcmVuZGVyZXI6ICdDYW52YXMgMkQnLAogICAgICAgICAgICAgICAgd2lkdGg6IGNhbnZhcy53aWR0aCwKICAgICAgICAgICAgICAgIGhlaWdodDogY2FudmFzLmhlaWdodAogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGluc3RhbGxMZWFmZXJDYW52YXNSZWFkeVdhdGNoKCkgewogICAgICAgICAgICB2YXIgYXR0ZW1wdHMgPSAwOwogICAgICAgICAgICB2YXIgbWF4QXR0ZW1wdHMgPSA2MDA7IC8vIOacgOmVv+e6piAzMCDnp5LvvIzmjIkgNTBtcyDmo4Dmn6UKICAgICAgICAgICAgdmFyIHRpbWVyID0gd2luZG93LnNldEludGVydmFsKGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGF0dGVtcHRzICs9IDE7CiAgICAgICAgICAgICAgbm90aWZ5U2NlbmVSZWFkeUlmTmVlZGVkKCk7CiAgICAgICAgICAgICAgaWYgKHNjZW5lUmVhZHlOb3RpZmllZCB8fCBhdHRlbXB0cyA+PSBtYXhBdHRlbXB0cykgewogICAgICAgICAgICAgICAgd2luZG93LmNsZWFySW50ZXJ2YWwodGltZXIpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfSwgNTApOwogICAgICAgICAgfQogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiA2LiDnp7vliqjnq6/op4blj6PlsLrlr7jlrojmiqQKICAgICAgICAgICAqIGlPUyAvIEFuZHJvaWQg55qE5Zyw5Z2A5qCP44CB6L2v6ZSu55uY44CB5pa55ZCR5YiH5o2i5Lya6K6pIGlmcmFtZSDop4blj6PlnKjpppbluKfkuYvlkI4KICAgICAgICAgICAqIOaJjeeos+WumuS4i+adpe+8m+i/memHjOWcqOinhuWPo+WPmOWMluaXtgogICAgICAgICAgICog5Li75Yqo5rS+5Y+RIHJlc2l6ZSDkuovku7bvvIzlubblu7bov5/ooaXlj5Hlh6DmrKHjgIIKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGluc3RhbGxWaWV3cG9ydFdhdGNoZG9nKCkgewogICAgICAgICAgICB2YXIgbGFzdFdpZHRoID0gLTE7CiAgICAgICAgICAgIHZhciBsYXN0SGVpZ2h0ID0gLTE7CgogICAgICAgICAgICBmdW5jdGlvbiBkaXNwYXRjaFJlc2l6ZShmb3JjZSkgewogICAgICAgICAgICAgIHZhciB3aWR0aCA9IHdpbmRvdy5pbm5lcldpZHRoIHx8IDA7CiAgICAgICAgICAgICAgdmFyIGhlaWdodCA9IHdpbmRvdy5pbm5lckhlaWdodCB8fCAwOwogICAgICAgICAgICAgIGlmICghZm9yY2UgJiYgd2lkdGggPT09IGxhc3RXaWR0aCAmJiBoZWlnaHQgPT09IGxhc3RIZWlnaHQpIHJldHVybjsKICAgICAgICAgICAgICBsYXN0V2lkdGggPSB3aWR0aDsKICAgICAgICAgICAgICBsYXN0SGVpZ2h0ID0gaGVpZ2h0OwogICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICB2YXIgZXZlbnQ7CiAgICAgICAgICAgICAgICBpZiAodHlwZW9mIHdpbmRvdy5FdmVudCA9PT0gJ2Z1bmN0aW9uJykgewogICAgICAgICAgICAgICAgICBldmVudCA9IG5ldyB3aW5kb3cuRXZlbnQoJ3Jlc2l6ZScpOwogICAgICAgICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgICAgICAgZXZlbnQgPSBkb2N1bWVudC5jcmVhdGVFdmVudCgnRXZlbnQnKTsKICAgICAgICAgICAgICAgICAgZXZlbnQuaW5pdEV2ZW50KCdyZXNpemUnLCBmYWxzZSwgZmFsc2UpOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgd2luZG93LmRpc3BhdGNoRXZlbnQoZXZlbnQpOwogICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICAvKiDlv73nlaUgKi8KICAgICAgICAgICAgICB9CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIGZ1bmN0aW9uIGNoZWNrQ2FudmFzU2l6ZSgpIHsKICAgICAgICAgICAgICB2YXIgY2FudmFzZXMgPSBkb2N1bWVudC5xdWVyeVNlbGVjdG9yQWxsKCdjYW52YXMnKTsKICAgICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IGNhbnZhc2VzLmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICAgICAgICBpZiAoIWNhbnZhc2VzW2ldLndpZHRoIHx8ICFjYW52YXNlc1tpXS5oZWlnaHQpIHsKICAgICAgICAgICAgICAgICAgZGlzcGF0Y2hSZXNpemUodHJ1ZSk7CiAgICAgICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICB9CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIGlmICh3aW5kb3cuUmVzaXplT2JzZXJ2ZXIpIHsKICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgdmFyIG9ic2VydmVyID0gbmV3IHdpbmRvdy5SZXNpemVPYnNlcnZlcihmdW5jdGlvbigpIHsKICAgICAgICAgICAgICAgICAgZGlzcGF0Y2hSZXNpemUoZmFsc2UpOwogICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgICAgICBvYnNlcnZlci5vYnNlcnZlKGRvY3VtZW50LmRvY3VtZW50RWxlbWVudCk7CiAgICAgICAgICAgICAgICBpZiAoZG9jdW1lbnQuYm9keSkgb2JzZXJ2ZXIub2JzZXJ2ZShkb2N1bWVudC5ib2R5KTsKICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgLyog5b+955WlICovCiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9CgogICAgICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcignb3JpZW50YXRpb25jaGFuZ2UnLCBmdW5jdGlvbigpIHsKICAgICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChmdW5jdGlvbigpIHsKICAgICAgICAgICAgICAgIGRpc3BhdGNoUmVzaXplKHRydWUpOwogICAgICAgICAgICAgIH0sIDI1MCk7CiAgICAgICAgICAgIH0pOwoKICAgICAgICAgICAgd2luZG93LmFkZEV2ZW50TGlzdGVuZXIoJ2xvYWQnLCBmdW5jdGlvbigpIHsKICAgICAgICAgICAgICBkaXNwYXRjaFJlc2l6ZSh0cnVlKTsKICAgICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChjaGVja0NhbnZhc1NpemUsIDMwMCk7CiAgICAgICAgICAgICAgd2luZG93LnNldFRpbWVvdXQoY2hlY2tDYW52YXNTaXplLCAxMjAwKTsKICAgICAgICAgICAgfSk7CgogICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChmdW5jdGlvbigpIHsKICAgICAgICAgICAgICBkaXNwYXRjaFJlc2l6ZSh0cnVlKTsKICAgICAgICAgICAgfSwgMTAwKTsKICAgICAgICAgICAgd2luZG93LnNldFRpbWVvdXQoZnVuY3Rpb24oKSB7CiAgICAgICAgICAgICAgZGlzcGF0Y2hSZXNpemUodHJ1ZSk7CiAgICAgICAgICAgIH0sIDUwMCk7CiAgICAgICAgICAgIHdpbmRvdy5zZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGRpc3BhdGNoUmVzaXplKHRydWUpOwogICAgICAgICAgICB9LCAxNTAwKTsKICAgICAgICAgIH0KICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogNy4g5oyH5Luk5YiG5Y+RCiAgICAgICAgICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tICovCiAgICAgICAgICBmdW5jdGlvbiBoYW5kbGVDb21tYW5kKGV2ZW50KSB7CiAgICAgICAgICAgIGlmICghZXZlbnQgfHwgIWV2ZW50LmRhdGEgfHwgZXZlbnQuZGF0YS5fX2xlYWZlckNhcHR1cmUgIT09IHRydWUpIHJldHVybjsKICAgICAgICAgICAgaWYgKGV2ZW50LmRhdGEuY29tbWFuZCAhPT0gdHJ1ZSkgcmV0dXJuOwogICAgICAgICAgICBpZiAod2luZG93LnBhcmVudCAmJiBldmVudC5zb3VyY2UgIT09IHdpbmRvdy5wYXJlbnQpIHJldHVybjsKCiAgICAgICAgICAgIHZhciByZXF1ZXN0ID0gZXZlbnQuZGF0YTsKICAgICAgICAgICAgdmFyIGlkID0gcmVxdWVzdC5pZDsKICAgICAgICAgICAgdmFyIGFjdGlvbiA9IHJlcXVlc3QuYWN0aW9uOwogICAgICAgICAgICB2YXIgcGF5bG9hZCA9IHJlcXVlc3QucGF5bG9hZCB8fCB7fTsKCiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgc3dpdGNoIChhY3Rpb24pIHsKICAgICAgICAgICAgICAgIGNhc2UgJ3BpbmcnOgogICAgICAgICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ3N1Y2Nlc3MnLCBpZCwgYWN0aW9uLCB7CiAgICAgICAgICAgICAgICAgICAgcmVhZHk6IHRydWUKICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgICAgICAgY2FzZSAnc2NyZWVuc2hvdCc6CiAgICAgICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnc3VjY2VzcycsIGlkLCBhY3Rpb24sIGNhcHR1cmVTY3JlZW5zaG90KHBheWxvYWQpKTsKICAgICAgICAgICAgICAgICAgYnJlYWs7CiAgICAgICAgICAgICAgICBjYXNlICdnZXRQYWdlRXJyb3JzJzoKICAgICAgICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdzdWNjZXNzJywgaWQsIGFjdGlvbiwgewogICAgICAgICAgICAgICAgICAgIHBhZ2VUb2tlbjogcGFnZVRva2VuLAogICAgICAgICAgICAgICAgICAgIGVycm9yczogcGFnZUVycm9ySGlzdG9yeS5zbGljZSgwKQogICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICAgICAgYnJlYWs7CiAgICAgICAgICAgICAgICBjYXNlICdnZXRTY2VuZVN0YXR1cyc6CiAgICAgICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnc3VjY2VzcycsIGlkLCBhY3Rpb24sIHsKICAgICAgICAgICAgICAgICAgICBzY2VuZVJlYWR5OiBzY2VuZVJlYWR5Tm90aWZpZWQKICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgICAgICAgZGVmYXVsdDoKICAgICAgICAgICAgICAgICAgcG9zdEVycm9yKGlkLCBhY3Rpb24sIG5ldyBFcnJvcign5pyq55+l55qE6aKE6KeI5oyH5Luk77yaJyArIGFjdGlvbiksICdVTktOT1dOX0FDVElPTicpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICBwb3N0RXJyb3IoaWQsIGFjdGlvbiwgZXJyb3IpOwogICAgICAgICAgICB9CiAgICAgICAgICB9CgogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiA4LiDliJ3lp4vljJYKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGFubm91bmNlUmVhZHkoKSB7CiAgICAgICAgICAgIHBvc3RUb1BhcmVudCgncmVhZHknLCBudWxsLCAncmVhZHknLCB7CiAgICAgICAgICAgICAgcmVhZHk6IHRydWUsCiAgICAgICAgICAgICAgY2FudmFzQ291bnQ6IGRvY3VtZW50LnF1ZXJ5U2VsZWN0b3JBbGwoJ2NhbnZhcycpLmxlbmd0aCwKICAgICAgICAgICAgICBzY2VuZVJlYWR5OiBzY2VuZVJlYWR5Tm90aWZpZWQKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9CgogICAgICAgICAgLyog5bC95pep5ZGK6K+J54i257uE5Lu26ISa5pys5bey57uP5omn6KGM77yb5b6u5L+h562J546v5aKD6Iul5oum5oiqIGRhdGE6IFVSTCDlhoXnmoTohJrmnKzvvIwKICAgICAgICAgICAqIOeItue7hOS7tuaUtuS4jeWIsOi/meadoea2iOaBr++8jOS8muiHquWKqOWIh+aNoiBpZnJhbWUg5Yqg6L295pa55byP44CCICovCiAgICAgICAgICBwb3N0VG9QYXJlbnQoJ2JyaWRnZS1hbGl2ZScsIG51bGwsICdicmlkZ2UnLCB7CiAgICAgICAgICAgIHJlYWR5OiB0cnVlLAogICAgICAgICAgICBtb2RlOiAnaWZyYW1lJwogICAgICAgICAgfSk7CgogICAgICAgICAgaW5zdGFsbExlYWZlckNhbnZhc1JlYWR5V2F0Y2goKTsKICAgICAgICAgIGluc3RhbGxWaWV3cG9ydFdhdGNoZG9nKCk7CiAgICAgICAgICBpbnN0YWxsUGFnZUVycm9ySGFuZGxlcnMoKTsKICAgICAgICAgIHN0YXJ0SW5qZWN0ZWRMb2FkaW5nV2F0Y2goKTsKCiAgICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcignbWVzc2FnZScsIGhhbmRsZUNvbW1hbmQsIGZhbHNlKTsKCiAgICAgICAgICBpZiAoZG9jdW1lbnQucmVhZHlTdGF0ZSA9PT0gJ2xvYWRpbmcnKSB7CiAgICAgICAgICAgIGRvY3VtZW50LmFkZEV2ZW50TGlzdGVuZXIoJ0RPTUNvbnRlbnRMb2FkZWQnLCBhbm5vdW5jZVJlYWR5LCBmYWxzZSk7CiAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICBhbm5vdW5jZVJlYWR5KCk7CiAgICAgICAgICB9CiAgICAgICAgfQogICAgICAgIHJldHVybiAnKCcgKyBsZWFmZXJDYXB0dXJlUnVudGltZS50b1N0cmluZygpICsgJykoKTsnOwogICAgICB9LAoKICAgICAgaW5qZWN0Q2FwdHVyZVJ1bnRpbWUoaHRtbCkgewogICAgICAgIGlmICh0eXBlb2YgaHRtbCAhPT0gJ3N0cmluZycgfHwgIWh0bWwudHJpbSgpKSByZXR1cm4gaHRtbDsKCiAgICAgICAgY29uc3QgbWFya2VyID0gJ2RhdGEtbGVhZmVyLWNhcHR1cmUnOwogICAgICAgIGlmIChodG1sLmluZGV4T2YobWFya2VyKSAhPT0gLTEpIHJldHVybiBodG1sOwoKICAgICAgICBjb25zdCBsb2FkaW5nU3R5bGUgPSBgCiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyB7CiAgcG9zaXRpb246IGZpeGVkOwogIHRvcDogMDsKICByaWdodDogMDsKICBib3R0b206IDA7CiAgbGVmdDogMDsKICB6LWluZGV4OiAyMTQ3NDgzMDAwOwogIGRpc3BsYXk6IGZsZXg7CiAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICBqdXN0aWZ5LWNvbnRlbnQ6IGNlbnRlcjsKICBib3gtc2l6aW5nOiBib3JkZXItYm94OwogIHBhZGRpbmc6IDI0cHg7CiAgYmFja2dyb3VuZDogcmdiYSg4LCAxMywgMjMsIDAuOTQpOwogIGNvbG9yOiAjZTJlOGYwOwogIGZvbnQtZmFtaWx5OiBzeXN0ZW0tdWksIC1hcHBsZS1zeXN0ZW0sICJQaW5nRmFuZyBTQyIsICJNaWNyb3NvZnQgWWFIZWkiLCBzYW5zLXNlcmlmOwogIHRyYW5zaXRpb246IG9wYWNpdHkgMC4zcyBlYXNlOwp9CiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ19jYXJkX18gewogIGRpc3BsYXk6IGZsZXg7CiAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICBnYXA6IDE0cHg7CiAgYm94LXNpemluZzogYm9yZGVyLWJveDsKICB3aWR0aDogbWF4LWNvbnRlbnQ7CiAgbWF4LXdpZHRoOiA0NDBweDsKICBwYWRkaW5nOiAxNnB4IDE4cHg7CiAgYm9yZGVyOiAxcHggc29saWQgcmdiYSgxNDgsIDE2MywgMTg0LCAwLjI0KTsKICBib3JkZXItcmFkaXVzOiAxNHB4OwogIGJhY2tncm91bmQ6IHJnYmEoMzAsIDQxLCA1OSwgMC45NCk7CiAgYm94LXNoYWRvdzogMCAyNHB4IDYwcHggLTIwcHggcmdiYSgwLCAwLCAwLCAwLjY1KTsKfQojX19sZWFmZXJfbG9hZGluZ19vdmVybGF5X18gLl9fbGVhZmVyX2xvYWRpbmdfc3Bpbm5lcl9fIHsKICBmbGV4LXNocmluazogMDsKICBib3gtc2l6aW5nOiBib3JkZXItYm94OwogIHdpZHRoOiAyOHB4OwogIGhlaWdodDogMjhweDsKICBib3JkZXI6IDNweCBzb2xpZCByZ2JhKDE0OCwgMTYzLCAxODQsIDAuMjgpOwogIGJvcmRlci10b3AtY29sb3I6ICMyMmQzZWU7CiAgYm9yZGVyLXJhZGl1czogOTk5OXB4OwogIGFuaW1hdGlvbjogX19sZWFmZXJfbG9hZGluZ19zcGluIDAuODVzIGxpbmVhciBpbmZpbml0ZTsKfQojX19sZWFmZXJfbG9hZGluZ19vdmVybGF5X18gLl9fbGVhZmVyX2xvYWRpbmdfY29weV9fIHsKICBkaXNwbGF5OiBmbGV4OwogIGZsZXgtZGlyZWN0aW9uOiBjb2x1bW47CiAgZ2FwOiAzcHg7CiAgbWluLXdpZHRoOiAwOwp9CiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ190aXRsZV9fIHsKICBjb2xvcjogI2Y4ZmFmYzsKICBmb250LXNpemU6IDEzcHg7CiAgZm9udC13ZWlnaHQ6IDYwMDsKICBsaW5lLWhlaWdodDogMS41Owp9CiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ19oaW50X18gewogIGNvbG9yOiAjOTRhM2I4OwogIGZvbnQtc2l6ZTogMTFweDsKICBsaW5lLWhlaWdodDogMS42OwogIG92ZXJmbG93LXdyYXA6IGFueXdoZXJlOwp9CiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ193ZWNoYXRfXyB7CiAgY29sb3I6ICM2NDc0OGI7CiAgZm9udC1zaXplOiAxMHB4OwogIGxpbmUtaGVpZ2h0OiAxLjY7Cn0KQGtleWZyYW1lcyBfX2xlYWZlcl9sb2FkaW5nX3NwaW4gewogIHRvIHsgdHJhbnNmb3JtOiByb3RhdGUoMzYwZGVnKTsgfQp9CkBtZWRpYSAobWF4LXdpZHRoOiA2NDBweCkgewogICNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyB7IHBhZGRpbmc6IDE2cHg7IH0KICAjX19sZWFmZXJfbG9hZGluZ19vdmVybGF5X18gLl9fbGVhZmVyX2xvYWRpbmdfY2FyZF9fIHsgbWF4LXdpZHRoOiAxMDAlOyBwYWRkaW5nOiAxNHB4IDE2cHg7IH0KfQpgOwoKICAgICAgICBjb25zdCBsb2FkaW5nT3ZlcmxheSA9IFsKICAgICAgICAgICcnLAogICAgICAgICAgJzwhLS0gbGVhZmVyIGxvYWRpbmcgb3ZlcmxheSBpbmplY3RlZCBiZWZvcmUgYmFzZTY0IGVuY29kaW5nIC0tPicsCiAgICAgICAgICAnPGRpdiBpZD0iX19sZWFmZXJfbG9hZGluZ19vdmVybGF5X18iIHJvbGU9InN0YXR1cyIgYXJpYS1saXZlPSJwb2xpdGUiPicsCiAgICAgICAgICAnICA8ZGl2IGNsYXNzPSJfX2xlYWZlcl9sb2FkaW5nX2NhcmRfXyI+JywKICAgICAgICAgICcgICAgPHNwYW4gY2xhc3M9Il9fbGVhZmVyX2xvYWRpbmdfc3Bpbm5lcl9fIiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9zcGFuPicsCiAgICAgICAgICAnICAgIDxkaXYgY2xhc3M9Il9fbGVhZmVyX2xvYWRpbmdfY29weV9fIj4nLAogICAgICAgICAgJyAgICAgIDxzdHJvbmcgaWQ9Il9fbGVhZmVyX2xvYWRpbmdfdGl0bGVfXyIgY2xhc3M9Il9fbGVhZmVyX2xvYWRpbmdfdGl0bGVfXyI+5q2j5Zyo5Yqg6L295rW35oqlPC9zdHJvbmc+JywKICAgICAgICAgICcgICAgICA8c3BhbiBpZD0iX19sZWFmZXJfbG9hZGluZ19oaW50X18iIGNsYXNzPSJfX2xlYWZlcl9sb2FkaW5nX2hpbnRfXyI+5q2j5Zyo5YeG5aSHIENhbnZhcyDnlLvluIPvvIzor7fnqI3lgJnigKY8L3NwYW4+JywKICAgICAgICAgICcgICAgICA8c3BhbiBjbGFzcz0iX19sZWFmZXJfbG9hZGluZ193ZWNoYXRfXyI+5b6u5L+h5Lit6Iul6ZW/5pe26Ze05peg5ZON5bqU77yM6K+354K55Y+z5LiK6KeSIMK3wrfCtyDpgInmi6nigJzlnKjmtY/op4jlmajmiZPlvIDigJ3jgII8L3NwYW4+JywKICAgICAgICAgICcgICAgPC9kaXY+JywKICAgICAgICAgICcgIDwvZGl2PicsCiAgICAgICAgICAnPC9kaXY+JywKICAgICAgICAgICcnCiAgICAgICAgXS5qb2luKCdcbicpOwoKICAgICAgICBjb25zdCBoZWFkSW5qZWN0aW9uID0gWwogICAgICAgICAgJycsCiAgICAgICAgICAnPCEtLSBsZWFmZXIgY2FwdHVyZSBicmlkZ2UgKyBsb2FkaW5nIFVJIGluamVjdGVkIGJlZm9yZSBiYXNlNjQgZW5jb2RpbmcgLS0+JywKICAgICAgICAgICc8c3R5bGUgaWQ9Il9fbGVhZmVyX2xvYWRpbmdfc3R5bGVfXyI+JyArIGxvYWRpbmdTdHlsZSArICc8L3N0eWxlPicsCiAgICAgICAgICAnPHNjcmlwdCAnICsgbWFya2VyICsgJz0iYnJpZGdlIj4nLAogICAgICAgICAgdGhpcy5idWlsZENhcHR1cmVSdW50aW1lU291cmNlKCksCiAgICAgICAgICAnPCcgKyAnL3NjcmlwdD4nLAogICAgICAgICAgJycKICAgICAgICBdLmpvaW4oJ1xuJyk7CgogICAgICAgIGxldCBvdXRwdXQgPSBodG1sOwoKICAgICAgICAvKiDlnKjmjIflrprlvIDmoIfnrb7kuYvlkI7mj5LlhaXniYfmrrXvvJvov5Tlm57mmK/lkKbmj5LlhaXmiJDlip/jgIIgKi8KICAgICAgICBjb25zdCBpbnNlcnRBZnRlclRhZyA9IChyZWdleCwgZnJhZ21lbnQpID0+IHsKICAgICAgICAgIGNvbnN0IG1hdGNoID0gcmVnZXguZXhlYyhvdXRwdXQpOwogICAgICAgICAgaWYgKCFtYXRjaCkgcmV0dXJuIGZhbHNlOwogICAgICAgICAgY29uc3QgaW5kZXggPSBtYXRjaC5pbmRleCArIG1hdGNoWzBdLmxlbmd0aDsKICAgICAgICAgIG91dHB1dCA9IG91dHB1dC5zbGljZSgwLCBpbmRleCkgKyBmcmFnbWVudCArIG91dHB1dC5zbGljZShpbmRleCk7CiAgICAgICAgICByZXR1cm4gdHJ1ZTsKICAgICAgICB9OwoKICAgICAgICAvKiDov5DooYzml7bku6PnoIHlv4XpobvlsL3ph4/ml6nmiafooYzvvIzkvJjlhYjmlL7lnKggaGVhZCDlhoXjgIIgKi8KICAgICAgICBpZiAoIWluc2VydEFmdGVyVGFnKC88aGVhZCg/OlxzW14+XSopPz4vaSwgaGVhZEluamVjdGlvbikpIHsKICAgICAgICAgIGlmICghaW5zZXJ0QWZ0ZXJUYWcoLzxodG1sKD86XHNbXj5dKik/Pi9pLCBoZWFkSW5qZWN0aW9uKSkgewogICAgICAgICAgICBpZiAoIWluc2VydEFmdGVyVGFnKC88IWRvY3R5cGVbXj5dKj4vaSwgaGVhZEluamVjdGlvbikpIHsKICAgICAgICAgICAgICBvdXRwdXQgPSBoZWFkSW5qZWN0aW9uICsgb3V0cHV0OwogICAgICAgICAgICB9CiAgICAgICAgICB9CiAgICAgICAgfQoKICAgICAgICAvKiDliqDovb3mj5DnpLrmlL7lnKggYm9keSDmnIDliY3pnaLvvIznoa7kv53ljbPkvb/kuJrliqHohJrmnKzmiqXplJnkuZ/og73lhYjnnIvliLDmj5DnpLrjgIIgKi8KICAgICAgICBpZiAoIWluc2VydEFmdGVyVGFnKC88Ym9keSg/OlxzW14+XSopPz4vaSwgbG9hZGluZ092ZXJsYXkpKSB7CiAgICAgICAgICBjb25zdCBib2R5Q2xvc2UgPSAvPFwvYm9keVxzKj4vaS5leGVjKG91dHB1dCk7CiAgICAgICAgICBpZiAoYm9keUNsb3NlKSB7CiAgICAgICAgICAgIG91dHB1dCA9IG91dHB1dC5zbGljZSgwLCBib2R5Q2xvc2UuaW5kZXgpICsgbG9hZGluZ092ZXJsYXkgKyBvdXRwdXQuc2xpY2UoYm9keUNsb3NlLmluZGV4KTsKICAgICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAgIGNvbnN0IGh0bWxDbG9zZSA9IC88XC9odG1sXHMqPi9pLmV4ZWMob3V0cHV0KTsKICAgICAgICAgICAgaWYgKGh0bWxDbG9zZSkgewogICAgICAgICAgICAgIG91dHB1dCA9IG91dHB1dC5zbGljZSgwLCBodG1sQ2xvc2UuaW5kZXgpICsgbG9hZGluZ092ZXJsYXkgKyBvdXRwdXQuc2xpY2UoaHRtbENsb3NlLmluZGV4KTsKICAgICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgICBvdXRwdXQgKz0gbG9hZGluZ092ZXJsYXk7CiAgICAgICAgICAgIH0KICAgICAgICAgIH0KICAgICAgICB9CgogICAgICAgIHJldHVybiBvdXRwdXQ7CiAgICAgIH0sCgogICAgICB0ZWFyZG93bkNhcHR1cmVCcmlkZ2UoKSB7CiAgICAgICAgaWYgKHRoaXMuYnJpZGdlVG9ybkRvd24pIHJldHVybjsKICAgICAgICB0aGlzLmJyaWRnZVRvcm5Eb3duID0gdHJ1ZTsKCiAgICAgICAgd2luZG93LnJlbW92ZUV2ZW50TGlzdGVuZXIoJ21lc3NhZ2UnLCB0aGlzLmhhbmRsZU1lc3NhZ2UpOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCdyZXNpemUnLCB0aGlzLm1lYXN1cmVTdGFnZSk7CiAgICAgICAgaWYgKHRoaXMuc3RhZ2VSZXNpemVPYnNlcnZlcikgewogICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgdGhpcy5zdGFnZVJlc2l6ZU9ic2VydmVyLmRpc2Nvbm5lY3QoKTsKICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgIC8qIOW/veeVpSAqLwogICAgICAgICAgfQogICAgICAgICAgdGhpcy5zdGFnZVJlc2l6ZU9ic2VydmVyID0gbnVsbDsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMucGFuU3RhcnQpIHRoaXMucGFuU3RhcnQgPSBudWxsOwogICAgICAgIHRoaXMuaXNQYW5uaW5nID0gZmFsc2U7CiAgICAgICAgdGhpcy5yZWplY3RQZW5kaW5nQ29tbWFuZHMoJ+e7hOS7tuW3sumUgOavgScpOwogICAgICAgIGlmICh0aGlzLnN0YXR1c1RpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5zdGF0dXNUaW1lcik7CiAgICAgICAgICB0aGlzLnN0YXR1c1RpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMuY29weVJlc2V0VGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLmNvcHlSZXNldFRpbWVyKTsKICAgICAgICAgIHRoaXMuY29weVJlc2V0VGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgICAgdGhpcy5jbGVhclNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgICB0aGlzLnJldm9rZUlmcmFtZUJsb2JVcmwoKTsKICAgICAgfSwKICAgICAgcmVzZXRQYWdlRXJyb3JzKCkgewogICAgICAgIHRoaXMucGFnZUVycm9ycyA9IFtdOwogICAgICAgIHRoaXMuZXJyb3JEaWFsb2dWaXNpYmxlID0gZmFsc2U7CiAgICAgICAgdGhpcy5jb3B5U3RhdGUgPSAnJzsKICAgICAgICB0aGlzLmVycm9yU2VxID0gMDsKICAgICAgICB0aGlzLnNlZW5QYWdlRXJyb3JJZHMgPSBPYmplY3QuY3JlYXRlKG51bGwpOwogICAgICAgIHRoaXMuZXJyb3JEaWFsb2dTdXBwcmVzc2VkVW50aWwgPSAwOwoKICAgICAgICBpZiAodGhpcy5jb3B5UmVzZXRUaW1lcikgewogICAgICAgICAgY2xlYXJUaW1lb3V0KHRoaXMuY29weVJlc2V0VGltZXIpOwogICAgICAgICAgdGhpcy5jb3B5UmVzZXRUaW1lciA9IG51bGw7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgLyoqCiAgICAgICAqIGlmcmFtZSDlnKjpobXpnaLliqDovb3ov4fnqIvkuK3lj6/og73lt7Lnu4/kuqfnlJ/lvILluLjvvIzkvYbnu4Tku7bnmoQgbWVzc2FnZSDnm5HlkKzlsJrmnKoKICAgICAgICog5bu656uL44CCaWZyYW1lIOWGheS8mue8k+WtmOacgOi/kSA1MCDmnaHlvILluLjvvIzov5nph4zlnKggbG9hZCDkuYvlkI7ooaXmi4nkuIDmrKHvvIwKICAgICAgICog6YWN5ZCIIHBhZ2VUb2tlbiArIHBhZ2VFcnJvcklkIOWOu+mHjeOAggogICAgICAgKi8KICAgICAgYXN5bmMgcmVxdWVzdFBhZ2VFcnJvcnMoKSB7CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IHJlc3VsdCA9IGF3YWl0IHRoaXMucG9zdENvbW1hbmQoJ2dldFBhZ2VFcnJvcnMnLCB7fSwgNTAwMCk7CiAgICAgICAgICBpZiAoIXJlc3VsdCB8fCAhQXJyYXkuaXNBcnJheShyZXN1bHQuZXJyb3JzKSkgcmV0dXJuOwogICAgICAgICAgZm9yIChsZXQgaSA9IDA7IGkgPCByZXN1bHQuZXJyb3JzLmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICAgIHRoaXMuaGFuZGxlUGFnZUVycm9yKHJlc3VsdC5lcnJvcnNbaV0pOwogICAgICAgICAgfQogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAvKiBpZnJhbWUg5bey5Y246L295oiW5bCa5pyq5bCx57uq5pe25b+955WlICovCiAgICAgICAgfQogICAgICB9LAoKICAgICAgaGFuZGxlUGFnZUVycm9yKGRhdGEpIHsKICAgICAgICBpZiAoIWRhdGEpIHJldHVybjsKCiAgICAgICAgY29uc3QgdHlwZSA9IGRhdGEudHlwZSB8fCAnZXJyb3InOwogICAgICAgIGNvbnN0IG1lc3NhZ2UgPSB0aGlzLnNhZmVFcnJvclRleHQoZGF0YS5tZXNzYWdlLCAyMDAwKSB8fCAn5pyq55+l5byC5bi4JzsKICAgICAgICBjb25zdCBzb3VyY2UgPSB0aGlzLnNhZmVFcnJvclRleHQoZGF0YS5zb3VyY2UsIDgwMCk7CiAgICAgICAgY29uc3Qgc3RhY2sgPSB0aGlzLnNhZmVFcnJvclRleHQoZGF0YS5zdGFjaywgNjAwMCk7CiAgICAgICAgY29uc3QgbGluZSA9IE51bWJlcihkYXRhLmxpbmUpIHx8IDA7CiAgICAgICAgY29uc3QgY29sdW1uID0gTnVtYmVyKGRhdGEuY29sdW1uKSB8fCAwOwogICAgICAgIGNvbnN0IHBhZ2VUb2tlbiA9IGRhdGEucGFnZVRva2VuIHx8ICd1bmtub3duJzsKICAgICAgICBjb25zdCBlcnJvcklkID0gZGF0YS5wYWdlRXJyb3JJZDsKCiAgICAgICAgbGV0IGRlZHVwZUtleSA9ICcnOwogICAgICAgIGlmIChlcnJvcklkKSB7CiAgICAgICAgICBkZWR1cGVLZXkgPSBwYWdlVG9rZW4gKyAnOicgKyBlcnJvcklkOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICBkZWR1cGVLZXkgPSAnZmFsbGJhY2s6JyArIHR5cGUgKyAnfCcgKyBtZXNzYWdlICsgJ3wnICsgc291cmNlICsgJ3wnICsgbGluZSArICd8JyArIGNvbHVtbjsKICAgICAgICB9CgogICAgICAgIGlmICh0aGlzLnNlZW5QYWdlRXJyb3JJZHNbZGVkdXBlS2V5XSkgcmV0dXJuOwogICAgICAgIHRoaXMuc2VlblBhZ2VFcnJvcklkc1tkZWR1cGVLZXldID0gdHJ1ZTsKCiAgICAgICAgdGhpcy5lcnJvclNlcSArPSAxOwogICAgICAgIGNvbnN0IGl0ZW0gPSB7CiAgICAgICAgICBpZDogdGhpcy5lcnJvclNlcSwKICAgICAgICAgIHR5cGU6IHR5cGUsCiAgICAgICAgICB0eXBlTGFiZWw6IHRoaXMuZXJyb3JUeXBlTGFiZWwodHlwZSksCiAgICAgICAgICBtZXNzYWdlOiBtZXNzYWdlLAogICAgICAgICAgc3RhY2s6IHN0YWNrLAogICAgICAgICAgbG9jYXRpb246IHRoaXMuZm9ybWF0RXJyb3JMb2NhdGlvbihzb3VyY2UsIGxpbmUsIGNvbHVtbiksCiAgICAgICAgICB0aW1lTGFiZWw6IHRoaXMuZm9ybWF0RXJyb3JUaW1lKGRhdGEudGltZXN0YW1wKSwKICAgICAgICAgIHRpbWVzdGFtcDogTnVtYmVyKGRhdGEudGltZXN0YW1wKSB8fCBEYXRlLm5vdygpCiAgICAgICAgfTsKCiAgICAgICAgdGhpcy5wYWdlRXJyb3JzID0gdGhpcy5wYWdlRXJyb3JzLmNvbmNhdChpdGVtKS5zbGljZSgtNTApOwoKICAgICAgICBpZiAoIXRoaXMuZXJyb3JEaWFsb2dWaXNpYmxlICYmIERhdGUubm93KCkgPj0gdGhpcy5lcnJvckRpYWxvZ1N1cHByZXNzZWRVbnRpbCkgewogICAgICAgICAgdGhpcy5lcnJvckRpYWxvZ1Zpc2libGUgPSB0cnVlOwogICAgICAgICAgdGhpcy5jb3B5U3RhdGUgPSAnJzsKICAgICAgICAgIHRoaXMuJG5leHRUaWNrKCgpID0+IHsKICAgICAgICAgICAgaWYgKHRoaXMuJHJlZnMuZXJyb3JEaWFsb2cgJiYgdGhpcy4kcmVmcy5lcnJvckRpYWxvZy5mb2N1cykgewogICAgICAgICAgICAgIHRoaXMuJHJlZnMuZXJyb3JEaWFsb2cuZm9jdXMoKTsKICAgICAgICAgICAgfQogICAgICAgICAgfSk7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgb3BlbkVycm9yRGlhbG9nKCkgewogICAgICAgIGlmICghdGhpcy5wYWdlRXJyb3JzLmxlbmd0aCkgcmV0dXJuOwogICAgICAgIHRoaXMuZXJyb3JEaWFsb2dWaXNpYmxlID0gdHJ1ZTsKICAgICAgICB0aGlzLmNvcHlTdGF0ZSA9ICcnOwogICAgICAgIHRoaXMuJG5leHRUaWNrKCgpID0+IHsKICAgICAgICAgIGlmICh0aGlzLiRyZWZzLmVycm9yRGlhbG9nICYmIHRoaXMuJHJlZnMuZXJyb3JEaWFsb2cuZm9jdXMpIHsKICAgICAgICAgICAgdGhpcy4kcmVmcy5lcnJvckRpYWxvZy5mb2N1cygpOwogICAgICAgICAgfQogICAgICAgIH0pOwogICAgICB9LAoKICAgICAgY2xvc2VFcnJvckRpYWxvZygpIHsKICAgICAgICB0aGlzLmVycm9yRGlhbG9nVmlzaWJsZSA9IGZhbHNlOwogICAgICAgIC8qIOeUqOaIt+S4u+WKqOWFs+mXreWQjuefreaaguaKkeWItuiHquWKqOW8ueeql++8jOmBv+WFjemhtemdouaMgee7reaKpemUmeaXtuW8ueeql+WPjeWkjeaKoueEpueCue+8mwogICAgICAgICAqIOaWsOW8guW4uOS7jeS8mue0r+iuoeWIsOW3peWFt+agj+eahOW8guW4uOiuoeaVsOS4reOAgiAqLwogICAgICAgIHRoaXMuZXJyb3JEaWFsb2dTdXBwcmVzc2VkVW50aWwgPSBEYXRlLm5vdygpICsgNTAwMDsKICAgICAgfSwKCgogICAgICBhc3luYyBjb3B5RXJyb3JJbmZvKCkgewogICAgICAgIGlmICghdGhpcy5wYWdlRXJyb3JzLmxlbmd0aCkgcmV0dXJuOwoKICAgICAgICB0aGlzLmNvcHlTdGF0ZSA9ICdjb3B5aW5nJzsKICAgICAgICBjb25zdCB0ZXh0ID0gdGhpcy5idWlsZEVycm9yQ29weVRleHQoKTsKICAgICAgICBsZXQgY29waWVkID0gZmFsc2U7CgogICAgICAgIHRyeSB7CiAgICAgICAgICBpZiAobmF2aWdhdG9yLmNsaXBib2FyZCAmJiB0eXBlb2YgbmF2aWdhdG9yLmNsaXBib2FyZC53cml0ZVRleHQgPT09ICdmdW5jdGlvbicpIHsKICAgICAgICAgICAgYXdhaXQgbmF2aWdhdG9yLmNsaXBib2FyZC53cml0ZVRleHQodGV4dCk7CiAgICAgICAgICAgIGNvcGllZCA9IHRydWU7CiAgICAgICAgICB9CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIGNvcGllZCA9IGZhbHNlOwogICAgICAgIH0KCiAgICAgICAgaWYgKCFjb3BpZWQpIGNvcGllZCA9IHRoaXMuY29weVRleHRGYWxsYmFjayh0ZXh0KTsKCiAgICAgICAgdGhpcy5jb3B5U3RhdGUgPSBjb3BpZWQgPyAnY29waWVkJyA6ICdmYWlsZWQnOwogICAgICAgIGlmICh0aGlzLmNvcHlSZXNldFRpbWVyKSBjbGVhclRpbWVvdXQodGhpcy5jb3B5UmVzZXRUaW1lcik7CiAgICAgICAgdGhpcy5jb3B5UmVzZXRUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgdGhpcy5jb3B5UmVzZXRUaW1lciA9IG51bGw7CiAgICAgICAgICB0aGlzLmNvcHlTdGF0ZSA9ICcnOwogICAgICAgIH0sIDI1MDApOwogICAgICB9LAoKICAgICAgY29weVRleHRGYWxsYmFjayh0ZXh0KSB7CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IHRleHRhcmVhID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgndGV4dGFyZWEnKTsKICAgICAgICAgIHRleHRhcmVhLnZhbHVlID0gdGV4dDsKICAgICAgICAgIHRleHRhcmVhLnNldEF0dHJpYnV0ZSgncmVhZG9ubHknLCAncmVhZG9ubHknKTsKICAgICAgICAgIHRleHRhcmVhLnN0eWxlLnBvc2l0aW9uID0gJ2ZpeGVkJzsKICAgICAgICAgIHRleHRhcmVhLnN0eWxlLnRvcCA9ICcwJzsKICAgICAgICAgIHRleHRhcmVhLnN0eWxlLmxlZnQgPSAnLTk5OTlweCc7CiAgICAgICAgICB0ZXh0YXJlYS5zdHlsZS5vcGFjaXR5ID0gJzAnOwogICAgICAgICAgZG9jdW1lbnQuYm9keS5hcHBlbmRDaGlsZCh0ZXh0YXJlYSk7CiAgICAgICAgICB0ZXh0YXJlYS5mb2N1cygpOwogICAgICAgICAgdGV4dGFyZWEuc2VsZWN0KCk7CiAgICAgICAgICB0ZXh0YXJlYS5zZXRTZWxlY3Rpb25SYW5nZSgwLCB0ZXh0YXJlYS52YWx1ZS5sZW5ndGgpOwogICAgICAgICAgY29uc3QgcmVzdWx0ID0gZG9jdW1lbnQuZXhlY0NvbW1hbmQoJ2NvcHknKTsKICAgICAgICAgIHRleHRhcmVhLnJlbW92ZSgpOwogICAgICAgICAgcmV0dXJuIHJlc3VsdDsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgcmV0dXJuIGZhbHNlOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIGJ1aWxkRXJyb3JDb3B5VGV4dCgpIHsKICAgICAgICBjb25zdCBsaW5lcyA9IFtdOwogICAgICAgIGxpbmVzLnB1c2goJ+mhtemdoui/kOihjOW8guW4uO+8iOWFsSAnICsgdGhpcy5wYWdlRXJyb3JzLmxlbmd0aCArICcg5p2h77yJJyk7CiAgICAgICAgbGluZXMucHVzaCgn5a+85Ye65pe26Ze077yaJyArIHRoaXMuZm9ybWF0RXJyb3JUaW1lKERhdGUubm93KCkpKTsKICAgICAgICBsaW5lcy5wdXNoKCcnKTsKCiAgICAgICAgZm9yIChsZXQgaSA9IDA7IGkgPCB0aGlzLnBhZ2VFcnJvcnMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgIGNvbnN0IGl0ZW0gPSB0aGlzLnBhZ2VFcnJvcnNbaV07CiAgICAgICAgICBsaW5lcy5wdXNoKCdbJyArIChpICsgMSkgKyAnXSAnICsgaXRlbS50aW1lTGFiZWwgKyAnIMK3ICcgKyBpdGVtLnR5cGVMYWJlbCk7CiAgICAgICAgICBsaW5lcy5wdXNoKGl0ZW0ubWVzc2FnZSk7CiAgICAgICAgICBpZiAoaXRlbS5sb2NhdGlvbikgbGluZXMucHVzaCgn5L2N572u77yaJyArIGl0ZW0ubG9jYXRpb24pOwogICAgICAgICAgaWYgKGl0ZW0uc3RhY2spIHsKICAgICAgICAgICAgbGluZXMucHVzaCgn5aCG5qCI77yaJyk7CiAgICAgICAgICAgIGxpbmVzLnB1c2goaXRlbS5zdGFjayk7CiAgICAgICAgICB9CiAgICAgICAgICBsaW5lcy5wdXNoKCctLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tJyk7CiAgICAgICAgfQoKICAgICAgICByZXR1cm4gbGluZXMuam9pbignXG4nKTsKICAgICAgfSwKCiAgICAgIGVycm9yVHlwZUxhYmVsKHR5cGUpIHsKICAgICAgICBzd2l0Y2ggKHR5cGUpIHsKICAgICAgICAgIGNhc2UgJ2Vycm9yJzoKICAgICAgICAgICAgcmV0dXJuICfohJrmnKzplJnor68nOwogICAgICAgICAgY2FzZSAncHJvbWlzZSc6CiAgICAgICAgICAgIHJldHVybiAnUHJvbWlzZSDlvILluLgnOwogICAgICAgICAgY2FzZSAncmVzb3VyY2UnOgogICAgICAgICAgICByZXR1cm4gJ+i1hOa6kOWKoOi9veWksei0pSc7CiAgICAgICAgICBjYXNlICdjYW52YXMnOgogICAgICAgICAgICByZXR1cm4gJ0NhbnZhcyDnlLvluIPlvILluLgnOwogICAgICAgICAgZGVmYXVsdDoKICAgICAgICAgICAgcmV0dXJuICfpobXpnaLlvILluLgnOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIGZvcm1hdEVycm9yVGltZSh0aW1lc3RhbXApIHsKICAgICAgICBjb25zdCB2YWx1ZSA9IE51bWJlcih0aW1lc3RhbXApOwogICAgICAgIGNvbnN0IGRhdGUgPSBuZXcgRGF0ZShpc0Zpbml0ZSh2YWx1ZSkgJiYgdmFsdWUgPiAwID8gdmFsdWUgOiBEYXRlLm5vdygpKTsKICAgICAgICBjb25zdCBwYWQgPSAobnVtYmVyKSA9PiBTdHJpbmcobnVtYmVyKS5wYWRTdGFydCgyLCAnMCcpOwogICAgICAgIHJldHVybiBwYWQoZGF0ZS5nZXRIb3VycygpKSArICc6JyArIHBhZChkYXRlLmdldE1pbnV0ZXMoKSkgKyAnOicgKyBwYWQoZGF0ZS5nZXRTZWNvbmRzKCkpOwogICAgICB9LAoKICAgICAgZm9ybWF0RXJyb3JMb2NhdGlvbihzb3VyY2UsIGxpbmUsIGNvbHVtbikgewogICAgICAgIGxldCBsb2NhdGlvbiA9IHNvdXJjZSB8fCAnJzsKICAgICAgICBpZiAobGluZSkgbG9jYXRpb24gKz0gKGxvY2F0aW9uID8gJzonIDogJycpICsgbGluZTsKICAgICAgICBpZiAoY29sdW1uKSBsb2NhdGlvbiArPSAobG9jYXRpb24gPyAnOicgOiAnJykgKyBjb2x1bW47CiAgICAgICAgcmV0dXJuIGxvY2F0aW9uOwogICAgICB9LAoKICAgICAgc2FmZUVycm9yVGV4dCh2YWx1ZSwgbWF4TGVuZ3RoKSB7CiAgICAgICAgaWYgKHZhbHVlID09PSBudWxsIHx8IHZhbHVlID09PSB1bmRlZmluZWQpIHJldHVybiAnJzsKICAgICAgICBsZXQgdGV4dCA9IFN0cmluZyh2YWx1ZSk7CiAgICAgICAgY29uc3QgbGltaXQgPSBtYXhMZW5ndGggfHwgMjAwMDsKICAgICAgICBpZiAodGV4dC5sZW5ndGggPiBsaW1pdCkgdGV4dCA9IHRleHQuc2xpY2UoMCwgbGltaXQpICsgJ+KApic7CiAgICAgICAgcmV0dXJuIHRleHQ7CiAgICAgIH0sCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAqIOa1t+aKpeWKoOi9veeKtuaAgQogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcmVzZXRTY2VuZUxvYWRpbmcoKSB7CiAgICAgICAgdGhpcy5zY2VuZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRTbG93ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRUaW1lZE91dCA9IGZhbHNlOwogICAgICAgIHRoaXMucGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4gPSBmYWxzZTsKICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgIH0sCgogICAgICBzdGFydFNjZW5lTG9hZGluZ1RpbWVycygpIHsKICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgICAgdGhpcy5zY2VuZVNsb3dUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHRoaXMuc2NlbmVMb2FkU2xvdyA9IHRydWU7CiAgICAgICAgfSwgNTAwMCk7CiAgICAgICAgdGhpcy5zY2VuZVRpbWVvdXRUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHsKICAgICAgICAgICAgdGhpcy5zY2VuZUxvYWRTbG93ID0gdHJ1ZTsKICAgICAgICAgICAgdGhpcy5zY2VuZUxvYWRUaW1lZE91dCA9IHRydWU7CiAgICAgICAgICB9CiAgICAgICAgfSwgMTIwMDApOwogICAgICAgIC8qIOiLpee7hOS7tuerr+eahCBzY2VuZS1yZWFkeSDmtojmga/ooqvlvq7kv6HnrYnnjq/looPmi6bmiKrvvIw0LjUg56eS5ZCO5YWI6K6p5Ye66YGu572p77yMCiAgICAgICAgICog5pi+56S6IGlmcmFtZSDlhoXpg6joh6rlt7Hms6jlhaXnmoTliqDovb0gLyDlpLHotKXmj5DnpLrjgIIgKi8KICAgICAgICB0aGlzLnNjZW5lUGFyZW50T3ZlcmxheVRpbWVyID0gc2V0VGltZW91dCgoKSA9PiB7CiAgICAgICAgICBpZiAoIXRoaXMuc2NlbmVSZWFkeSkgdGhpcy5wYXJlbnRMb2FkaW5nT3ZlcmxheUhpZGRlbiA9IHRydWU7CiAgICAgICAgfSwgNDUwMCk7CiAgICAgIH0sCgogICAgICBjbGVhclNjZW5lTG9hZGluZ1RpbWVycygpIHsKICAgICAgICBpZiAodGhpcy5zY2VuZVNsb3dUaW1lcikgewogICAgICAgICAgY2xlYXJUaW1lb3V0KHRoaXMuc2NlbmVTbG93VGltZXIpOwogICAgICAgICAgdGhpcy5zY2VuZVNsb3dUaW1lciA9IG51bGw7CiAgICAgICAgfQogICAgICAgIGlmICh0aGlzLnNjZW5lVGltZW91dFRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5zY2VuZVRpbWVvdXRUaW1lcik7CiAgICAgICAgICB0aGlzLnNjZW5lVGltZW91dFRpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMuc2NlbmVQYXJlbnRPdmVybGF5VGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnNjZW5lUGFyZW50T3ZlcmxheVRpbWVyKTsKICAgICAgICAgIHRoaXMuc2NlbmVQYXJlbnRPdmVybGF5VGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIG1hcmtTY2VuZVJlYWR5KCkgewogICAgICAgIHRoaXMuc2NlbmVSZWFkeSA9IHRydWU7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRTbG93ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRUaW1lZE91dCA9IGZhbHNlOwogICAgICAgIHRoaXMucGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4gPSBmYWxzZTsKICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgIH0sCgogICAgICBhc3luYyByZXF1ZXN0U2NlbmVTdGF0dXMoKSB7CiAgICAgICAgaWYgKCF0aGlzLmlmcmFtZVJlYWR5KSByZXR1cm47CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IHJlc3VsdCA9IGF3YWl0IHRoaXMucG9zdENvbW1hbmQoJ2dldFNjZW5lU3RhdHVzJywge30sIDQwMDApOwogICAgICAgICAgaWYgKHJlc3VsdCAmJiByZXN1bHQuc2NlbmVSZWFkeSkgdGhpcy5tYXJrU2NlbmVSZWFkeSgpOwogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAvKiBpZnJhbWUg5bCa5pyq6L+Q6KGM5oiW5bey5Y246L295pe25b+955WlICovCiAgICAgICAgfQogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICogaWZyYW1lIOadpea6kOWFvOWuueaooeW8jwogICAgICAgKiDlvq7kv6HnrYnlhoXnva7mtY/op4jlmajlj6/og73muLLmn5MgZGF0YTogVVJMIGlmcmFtZSDnmoQgSFRNTO+8jOWNtOS4jeaJp+ihjOWFtuS4reeahAogICAgICAgKiDohJrmnKzvvIjooajnjrDkuLrnmb3lsY8gLyDnlLvluIPkuI3lh7rnjrDvvInjgILov5nph4znlKggYnJpZGdlLWFsaXZlIOaOoua1i+iEmuacrOaYr+WQpgogICAgICAgKiDmiafooYzvvIzlpLHotKXml7bkvp3mrKHpmY3nuqfvvJpkYXRhIFVSTCDihpIgQmxvYiBVUkwg4oaSIHNyY2RvY+OAggogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgY2xlYXJTb3VyY2VGYWxsYmFja0NoZWNrKCkgewogICAgICAgIGlmICh0aGlzLnNvdXJjZUZhbGxiYWNrVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnNvdXJjZUZhbGxiYWNrVGltZXIpOwogICAgICAgICAgdGhpcy5zb3VyY2VGYWxsYmFja1RpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBzY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKSB7CiAgICAgICAgdGhpcy5jbGVhclNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgICB0aGlzLnNvdXJjZUZhbGxiYWNrVGltZXIgPSBzZXRUaW1lb3V0KCgpID0+IHsKICAgICAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tUaW1lciA9IG51bGw7CiAgICAgICAgICBpZiAodGhpcy5icmlkZ2VBbGl2ZSB8fCB0aGlzLnNjZW5lUmVhZHkgfHwgdGhpcy5icmlkZ2VUb3JuRG93biB8fCB0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkKSB7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KICAgICAgICAgIHRoaXMudHJ5TmV4dElmcmFtZVNvdXJjZSgpOwogICAgICAgIH0sIDM1MDApOwogICAgICB9LAoKICAgICAgcmV2b2tlSWZyYW1lQmxvYlVybCgpIHsKICAgICAgICBpZiAodGhpcy5pZnJhbWVCbG9iVXJsKSB7CiAgICAgICAgICB0cnkgewogICAgICAgICAgICBVUkwucmV2b2tlT2JqZWN0VVJMKHRoaXMuaWZyYW1lQmxvYlVybCk7CiAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAvKiDlv73nlaUgKi8KICAgICAgICAgIH0KICAgICAgICAgIHRoaXMuaWZyYW1lQmxvYlVybCA9ICcnOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIHByZXBhcmVGb3JJZnJhbWVTb3VyY2VDaGFuZ2UoKSB7CiAgICAgICAgdGhpcy5pZnJhbWVSZWFkeSA9IGZhbHNlOwogICAgICAgIHRoaXMuc2NlbmVSZWFkeSA9IGZhbHNlOwogICAgICAgIHRoaXMuYnJpZGdlQWxpdmUgPSBmYWxzZTsKICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAnJzsKICAgICAgICB0aGlzLnJlc2V0U2NlbmVMb2FkaW5nKCk7CiAgICAgICAgdGhpcy5yZXNldFBhZ2VFcnJvcnMoKTsKICAgICAgICB0aGlzLmNsZWFyU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICAgIHRoaXMucmVqZWN0UGVuZGluZ0NvbW1hbmRzKCfmraPlnKjliIfmjaLpooTop4jlhbzlrrnmqKHlvI8nKTsKICAgICAgfSwKCiAgICAgIHRyeU5leHRJZnJhbWVTb3VyY2UoKSB7CiAgICAgICAgaWYgKHRoaXMuYnJpZGdlQWxpdmUpIHJldHVybjsKCiAgICAgICAgaWYgKHRoaXMuaWZyYW1lU291cmNlTW9kZSA9PT0gJ2RhdGEnKSB7CiAgICAgICAgICB0aGlzLnN3aXRjaElmcmFtZVNvdXJjZSgnYmxvYicpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5pZnJhbWVTb3VyY2VNb2RlID09PSAnYmxvYicpIHsKICAgICAgICAgIHRoaXMuc3dpdGNoSWZyYW1lU291cmNlKCdzcmNkb2MnKTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tGYWlsZWQgPSB0cnVlOwogICAgICAgIHRoaXMucGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4gPSBmYWxzZTsKICAgICAgICB0aGlzLmhhbmRsZVBhZ2VFcnJvcih7CiAgICAgICAgICBwYWdlVG9rZW46ICdzb3VyY2UtZmFsbGJhY2stJyArIERhdGUubm93KCksCiAgICAgICAgICBwYWdlRXJyb3JJZDogMSwKICAgICAgICAgIHR5cGU6ICdlcnJvcicsCiAgICAgICAgICBtZXNzYWdlOiAn5b2T5YmN5rWP6KeI5Zmo5peg5rOV5omn6KGM6aKE6KeI6ISa5pys77yI5bey5bCd6K+VIGRhdGEgVVJM44CBQmxvYuOAgXNyY2RvY++8ie+8jOivt+WwneivleWcqOezu+e7n+a1j+iniOWZqOS4reaJk+W8gOOAgicsCiAgICAgICAgICB0aW1lc3RhbXA6IERhdGUubm93KCkKICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIHN3aXRjaElmcmFtZVNvdXJjZShtb2RlKSB7CiAgICAgICAgdGhpcy5jbGVhclNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgICB0aGlzLnByZXBhcmVGb3JJZnJhbWVTb3VyY2VDaGFuZ2UoKTsKCiAgICAgICAgaWYgKG1vZGUgPT09ICdibG9iJykgewogICAgICAgICAgdGhpcy5yZXZva2VJZnJhbWVCbG9iVXJsKCk7CiAgICAgICAgICB0cnkgewogICAgICAgICAgICBjb25zdCBibG9iID0gbmV3IEJsb2IoW3RoaXMuaW5qZWN0ZWRIdG1sXSwgewogICAgICAgICAgICAgIHR5cGU6ICd0ZXh0L2h0bWw7Y2hhcnNldD11dGYtOCcKICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIHRoaXMuaWZyYW1lQmxvYlVybCA9IFVSTC5jcmVhdGVPYmplY3RVUkwoYmxvYik7CiAgICAgICAgICAgIHRoaXMuaWZyYW1lU291cmNlTW9kZSA9ICdibG9iJzsKICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgIHRoaXMuaWZyYW1lU291cmNlTW9kZSA9ICdkYXRhJzsKICAgICAgICAgICAgdGhpcy5zb3VyY2VGYWxsYmFja0ZhaWxlZCA9IHRydWU7CiAgICAgICAgICAgIHRoaXMucGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4gPSBmYWxzZTsKICAgICAgICAgIH0KICAgICAgICB9IGVsc2UgaWYgKG1vZGUgPT09ICdzcmNkb2MnKSB7CiAgICAgICAgICB0aGlzLnJldm9rZUlmcmFtZUJsb2JVcmwoKTsKICAgICAgICAgIHRoaXMuaWZyYW1lU291cmNlTW9kZSA9ICdzcmNkb2MnOwogICAgICAgIH0KCiAgICAgICAgdGhpcy5zY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgfSwKCiAgICAgIGhhbmRsZUJyaWRnZUFsaXZlKCkgewogICAgICAgIHRoaXMuYnJpZGdlQWxpdmUgPSB0cnVlOwogICAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tGYWlsZWQgPSBmYWxzZTsKICAgICAgICB0aGlzLmNsZWFyU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICogaWZyYW1lIOa2iOaBr+mAmumBkwogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgaGFuZGxlSWZyYW1lTG9hZCgpIHsKICAgICAgICB0aGlzLmlmcmFtZVJlYWR5ID0gdHJ1ZTsKICAgICAgICB0aGlzLnJlamVjdFBlbmRpbmdDb21tYW5kcygnaWZyYW1lIOW3sumHjeaWsOWKoOi9vScpOwogICAgICAgIHRoaXMucmVxdWVzdFBhZ2VFcnJvcnMoKTsKICAgICAgICBpZiAoIXRoaXMuc2NlbmVSZWFkeSkgdGhpcy5zdGFydFNjZW5lTG9hZGluZ1RpbWVycygpOwogICAgICAgIHRoaXMucmVxdWVzdFNjZW5lU3RhdHVzKCk7CiAgICAgICAgaWYgKHRoaXMuYnJpZGdlQWxpdmUpIHRoaXMuY2xlYXJTb3VyY2VGYWxsYmFja0NoZWNrKCk7CiAgICAgICAgZWxzZSB0aGlzLnNjaGVkdWxlU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICB9LAogICAgICAvKioKICAgICAgICog5ZCRIGlmcmFtZSDlj5HpgIHmiKrlm77mjIfku6TvvIzlubbnrYnlvoUgaWZyYW1lIOWbnuS8oOe7k+aenOOAggogICAgICAgKi8KICAgICAgcG9zdENvbW1hbmQoYWN0aW9uLCBwYXlsb2FkLCB0aW1lb3V0TXMpIHsKICAgICAgICByZXR1cm4gbmV3IFByb21pc2UoKHJlc29sdmUsIHJlamVjdCkgPT4gewogICAgICAgICAgY29uc3QgZnJhbWUgPSB0aGlzLiRyZWZzLnByZXZpZXdJZnJhbWU7CiAgICAgICAgICBpZiAoIWZyYW1lIHx8ICFmcmFtZS5jb250ZW50V2luZG93KSB7CiAgICAgICAgICAgIHJlamVjdChuZXcgRXJyb3IoJ+mihOiniOWwmuacquWKoOi9veWujOaIkCcpKTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQogICAgICAgICAgaWYgKCF0aGlzLmlmcmFtZVJlYWR5KSB7CiAgICAgICAgICAgIHJlamVjdChuZXcgRXJyb3IoJ+mihOiniOWwmuacquWKoOi9veWujOaIkO+8jOivt+eojeWQjuWGjeivlScpKTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQoKICAgICAgICAgIGNvbnN0IGlkID0gJ3RsLScgKyBEYXRlLm5vdygpLnRvU3RyaW5nKDM2KSArICctJyArICgrK3RoaXMuY29tbWFuZFNlcSk7CiAgICAgICAgICBjb25zdCB0aW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgICBjb25zdCBwZW5kaW5nID0gdGhpcy5wZW5kaW5nQ29tbWFuZHNbaWRdOwogICAgICAgICAgICBpZiAoIXBlbmRpbmcpIHJldHVybjsKICAgICAgICAgICAgZGVsZXRlIHRoaXMucGVuZGluZ0NvbW1hbmRzW2lkXTsKICAgICAgICAgICAgcGVuZGluZy5yZWplY3QobmV3IEVycm9yKCfmjIfku6TmiafooYzotoXml7bvvJonICsgYWN0aW9uKSk7CiAgICAgICAgICB9LCB0aW1lb3V0TXMgfHwgMjAwMDApOwoKICAgICAgICAgIHRoaXMucGVuZGluZ0NvbW1hbmRzW2lkXSA9IHsKICAgICAgICAgICAgcmVzb2x2ZSwKICAgICAgICAgICAgcmVqZWN0LAogICAgICAgICAgICB0aW1lcgogICAgICAgICAgfTsKCiAgICAgICAgICB0cnkgewogICAgICAgICAgICBmcmFtZS5jb250ZW50V2luZG93LnBvc3RNZXNzYWdlKHsKICAgICAgICAgICAgICBfX2xlYWZlckNhcHR1cmU6IHRydWUsCiAgICAgICAgICAgICAgY29tbWFuZDogdHJ1ZSwKICAgICAgICAgICAgICBpZDogaWQsCiAgICAgICAgICAgICAgYWN0aW9uOiBhY3Rpb24sCiAgICAgICAgICAgICAgcGF5bG9hZDogcGF5bG9hZCB8fCB7fQogICAgICAgICAgICB9LCAnKicpOwogICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgY2xlYXJUaW1lb3V0KHRpbWVyKTsKICAgICAgICAgICAgZGVsZXRlIHRoaXMucGVuZGluZ0NvbW1hbmRzW2lkXTsKICAgICAgICAgICAgcmVqZWN0KGVycm9yKTsKICAgICAgICAgIH0KICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIHJlamVjdFBlbmRpbmdDb21tYW5kcyhyZWFzb24pIHsKICAgICAgICBjb25zdCBpZHMgPSBPYmplY3Qua2V5cyh0aGlzLnBlbmRpbmdDb21tYW5kcyB8fCB7fSk7CiAgICAgICAgZm9yIChsZXQgaSA9IDA7IGkgPCBpZHMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgIGNvbnN0IHBlbmRpbmcgPSB0aGlzLnBlbmRpbmdDb21tYW5kc1tpZHNbaV1dOwogICAgICAgICAgZGVsZXRlIHRoaXMucGVuZGluZ0NvbW1hbmRzW2lkc1tpXV07CiAgICAgICAgICBpZiAoIXBlbmRpbmcpIGNvbnRpbnVlOwogICAgICAgICAgY2xlYXJUaW1lb3V0KHBlbmRpbmcudGltZXIpOwogICAgICAgICAgcGVuZGluZy5yZWplY3QobmV3IEVycm9yKHJlYXNvbiB8fCAn5oyH5Luk5bey5Y+W5raIJykpOwogICAgICAgIH0KICAgICAgfSwKICAgICAgZG93bmxvYWRVcmwodXJsLCBmaWxlbmFtZSkgewogICAgICAgIGlmICghdXJsKSByZXR1cm47CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IGxpbmsgPSBkb2N1bWVudC5jcmVhdGVFbGVtZW50KCdhJyk7CiAgICAgICAgICBsaW5rLmhyZWYgPSB1cmw7CiAgICAgICAgICBsaW5rLmRvd25sb2FkID0gZmlsZW5hbWUgfHwgJ2Rvd25sb2FkJzsKICAgICAgICAgIGxpbmsucmVsID0gJ25vb3BlbmVyJzsKICAgICAgICAgIGxpbmsuc3R5bGUuZGlzcGxheSA9ICdub25lJzsKICAgICAgICAgIGRvY3VtZW50LmJvZHkuYXBwZW5kQ2hpbGQobGluayk7CiAgICAgICAgICBsaW5rLmNsaWNrKCk7CiAgICAgICAgICBsaW5rLnJlbW92ZSgpOwogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICBjb25zb2xlLndhcm4oJ1tMZWFmZXJKUy1WaWV3ZXJdIGF1dG8gZG93bmxvYWQgZmFpbGVkOicsIGVycm9yKTsKICAgICAgICB9CiAgICAgIH0sCgoKICAgICAgZ2NkKGEsIGIpIHsKICAgICAgICBsZXQgeCA9IE1hdGguYWJzKE1hdGgucm91bmQoYSkpOwogICAgICAgIGxldCB5ID0gTWF0aC5hYnMoTWF0aC5yb3VuZChiKSk7CiAgICAgICAgd2hpbGUgKHkpIHsKICAgICAgICAgIGNvbnN0IHJlbWFpbmRlciA9IHggJSB5OwogICAgICAgICAgeCA9IHk7CiAgICAgICAgICB5ID0gcmVtYWluZGVyOwogICAgICAgIH0KICAgICAgICByZXR1cm4geCB8fCAxOwogICAgICB9LAoKICAgICAgLyoqCiAgICAgICAqIOWbvueJh+e7n+S4gOS9v+eUqCBhY2NvdW50RGF0YS50aXRsZSDkvZzkuLrmlofku7blkI3vvJoKICAgICAgICogICDlhrDmmbbop4nphpIgwrcg5rW35oqlLnBuZyAvIC5qcGcKICAgICAgICog5Y+q5pu/5o2i5pON5L2c57O757uf5LiN5YWB6K6455qE5a2X56ym77yM5L+d55WZ5Lit5paH5LiO56m65qC877yb6YeN5ZCN5pe255Sx5rWP6KeI5Zmo6Ieq5Yqo6L+95Yqg5bqP5Y+344CCCiAgICAgICAqLwogICAgICBidWlsZFRpdGxlRmlsZU5hbWUoZXh0ZW5zaW9uKSB7CiAgICAgICAgY29uc3QgcmF3VGl0bGUgPSB0aGlzLmFjY291bnREYXRhICYmIHRoaXMuYWNjb3VudERhdGEudGl0bGUgPwogICAgICAgICAgU3RyaW5nKHRoaXMuYWNjb3VudERhdGEudGl0bGUpIDoKICAgICAgICAgICfmtbfmiqXkvZzlk4EnOwogICAgICAgIGNvbnN0IHNhZmVUaXRsZSA9IHJhd1RpdGxlCiAgICAgICAgICAucmVwbGFjZSgvW1xcLzoqPyI8PnxcclxuXSsvZywgJy0nKQogICAgICAgICAgLnJlcGxhY2UoL1xzKy9nLCAnICcpCiAgICAgICAgICAudHJpbSgpCiAgICAgICAgICAucmVwbGFjZSgvXlwuK3xcLiskL2csICcnKQogICAgICAgICAgLnNsaWNlKDAsIDgwKQogICAgICAgICAgLnRyaW0oKTsKICAgICAgICBjb25zdCBzYWZlRXh0ZW5zaW9uID0gU3RyaW5nKGV4dGVuc2lvbiB8fCAncG5nJykucmVwbGFjZSgvXlwuKy8sICcnKSB8fCAncG5nJzsKICAgICAgICByZXR1cm4gKHNhZmVUaXRsZSB8fCAn5rW35oql5L2c5ZOBJykgKyAnLicgKyBzYWZlRXh0ZW5zaW9uOwogICAgICB9LAoKCiAgICAgIGVycm9yTWVzc2FnZShlcnJvcikgewogICAgICAgIGlmICghZXJyb3IpIHJldHVybiAn5pyq55+l6ZSZ6K+vJzsKICAgICAgICBpZiAoZXJyb3IubWVzc2FnZSkgcmV0dXJuIGVycm9yLm1lc3NhZ2U7CiAgICAgICAgaWYgKGVycm9yLmRhdGEgJiYgZXJyb3IuZGF0YS5tZXNzYWdlKSByZXR1cm4gZXJyb3IuZGF0YS5tZXNzYWdlOwogICAgICAgIHJldHVybiBTdHJpbmcoZXJyb3IpOwogICAgICB9LAoKICAgICAgc2NoZWR1bGVTdGF0dXNSZXNldChkZWxheSkgewogICAgICAgIGlmICh0aGlzLnN0YXR1c1RpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5zdGF0dXNUaW1lcik7CiAgICAgICAgICB0aGlzLnN0YXR1c1RpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgICAgdGhpcy5zdGF0dXNUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgdGhpcy5zdGF0dXNUaW1lciA9IG51bGw7CiAgICAgICAgICBpZiAoIXRoaXMuZXhwb3J0QnVzeSkgewogICAgICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAnJzsKICAgICAgICAgIH0KICAgICAgICB9LCBkZWxheSB8fCAzMDAwKTsKICAgICAgfSwKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICogaWZyYW1lIOa2iOaBr+mAmumBkwogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgaGFuZGxlTWVzc2FnZShldmVudCkgewogICAgICAgIGNvbnN0IG1lc3NhZ2UgPSBldmVudCAmJiBldmVudC5kYXRhOwogICAgICAgIGlmICghbWVzc2FnZSB8fCBtZXNzYWdlLl9fbGVhZmVyQ2FwdHVyZSAhPT0gdHJ1ZSkgcmV0dXJuOwogICAgICAgIGlmIChtZXNzYWdlLnNvdXJjZSAhPT0gJ2xlYWZlci1jYXB0dXJlJykgcmV0dXJuOwogICAgICAgIGlmIChtZXNzYWdlLmNvbW1hbmQgPT09IHRydWUpIHJldHVybjsKCiAgICAgICAgY29uc3QgZnJhbWUgPSB0aGlzLiRyZWZzLnByZXZpZXdJZnJhbWU7CiAgICAgICAgaWYgKCFmcmFtZSB8fCAhZnJhbWUuY29udGVudFdpbmRvdyB8fCBldmVudC5zb3VyY2UgIT09IGZyYW1lLmNvbnRlbnRXaW5kb3cpIHJldHVybjsKCiAgICAgICAgaWYgKG1lc3NhZ2UuZXZlbnQgPT09ICdicmlkZ2UtYWxpdmUnKSB7CiAgICAgICAgICB0aGlzLmhhbmRsZUJyaWRnZUFsaXZlKCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobWVzc2FnZS5ldmVudCA9PT0gJ3JlYWR5JykgewogICAgICAgICAgdGhpcy5pZnJhbWVSZWFkeSA9IHRydWU7CiAgICAgICAgICB0aGlzLmhhbmRsZUJyaWRnZUFsaXZlKCk7CiAgICAgICAgICBpZiAobWVzc2FnZS5kYXRhICYmIG1lc3NhZ2UuZGF0YS5zY2VuZVJlYWR5KSB0aGlzLm1hcmtTY2VuZVJlYWR5KCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobWVzc2FnZS5ldmVudCA9PT0gJ3NjZW5lLXJlYWR5JykgewogICAgICAgICAgdGhpcy5oYW5kbGVCcmlkZ2VBbGl2ZSgpOwogICAgICAgICAgdGhpcy5tYXJrU2NlbmVSZWFkeSgpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgaWYgKG1lc3NhZ2UuZXZlbnQgPT09ICdwYWdlLWVycm9yJykgewogICAgICAgICAgdGhpcy5oYW5kbGVQYWdlRXJyb3IobWVzc2FnZS5kYXRhKTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IHBlbmRpbmcgPSB0aGlzLnBlbmRpbmdDb21tYW5kc1ttZXNzYWdlLmlkXTsKICAgICAgICBpZiAocGVuZGluZykgewogICAgICAgICAgZGVsZXRlIHRoaXMucGVuZGluZ0NvbW1hbmRzW21lc3NhZ2UuaWRdOwogICAgICAgICAgY2xlYXJUaW1lb3V0KHBlbmRpbmcudGltZXIpOwogICAgICAgICAgaWYgKG1lc3NhZ2UuZXZlbnQgPT09ICdzdWNjZXNzJykgewogICAgICAgICAgICBwZW5kaW5nLnJlc29sdmUobWVzc2FnZS5kYXRhKTsKICAgICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAgIHBlbmRpbmcucmVqZWN0KG5ldyBFcnJvcigKICAgICAgICAgICAgICBtZXNzYWdlLmRhdGEgJiYgbWVzc2FnZS5kYXRhLm1lc3NhZ2UgPyBtZXNzYWdlLmRhdGEubWVzc2FnZSA6ICdpZnJhbWUg5omn6KGM5oyH5Luk5aSx6LSlJwogICAgICAgICAgICApKTsKICAgICAgICAgIH0KICAgICAgICB9CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgKiDnvKnmlL4gLyDlubPnp7sKICAgICAgICogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIG9ic2VydmVTdGFnZSgpIHsKICAgICAgICB0aGlzLm1lYXN1cmVTdGFnZSgpOwogICAgICAgIGlmICh0eXBlb2YgUmVzaXplT2JzZXJ2ZXIgIT09ICdmdW5jdGlvbicgfHwgIXRoaXMuJHJlZnMuc3RhZ2UpIHJldHVybjsKICAgICAgICB0cnkgewogICAgICAgICAgdGhpcy5zdGFnZVJlc2l6ZU9ic2VydmVyID0gbmV3IFJlc2l6ZU9ic2VydmVyKCgpID0+IHRoaXMubWVhc3VyZVN0YWdlKCkpOwogICAgICAgICAgdGhpcy5zdGFnZVJlc2l6ZU9ic2VydmVyLm9ic2VydmUodGhpcy4kcmVmcy5zdGFnZSk7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIHRoaXMuc3RhZ2VSZXNpemVPYnNlcnZlciA9IG51bGw7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgbWVhc3VyZVN0YWdlKCkgewogICAgICAgIGNvbnN0IHN0YWdlID0gdGhpcy4kcmVmcy5zdGFnZTsKICAgICAgICBpZiAoIXN0YWdlKSByZXR1cm47CgogICAgICAgIGxldCBob3Jpem9udGFsUGFkZGluZyA9IDA7CiAgICAgICAgbGV0IHZlcnRpY2FsUGFkZGluZyA9IDA7CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IHN0eWxlcyA9IHdpbmRvdy5nZXRDb21wdXRlZFN0eWxlKHN0YWdlKTsKICAgICAgICAgIGhvcml6b250YWxQYWRkaW5nID0gKHBhcnNlRmxvYXQoc3R5bGVzLnBhZGRpbmdMZWZ0KSB8fCAwKSArIChwYXJzZUZsb2F0KHN0eWxlcy5wYWRkaW5nUmlnaHQpIHx8IDApOwogICAgICAgICAgdmVydGljYWxQYWRkaW5nID0gKHBhcnNlRmxvYXQoc3R5bGVzLnBhZGRpbmdUb3ApIHx8IDApICsgKHBhcnNlRmxvYXQoc3R5bGVzLnBhZGRpbmdCb3R0b20pIHx8IDApOwogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAvKiDlj5bkuI3liLDorqHnrpfmoLflvI/ml7bmjInml6DlhoXovrnot53lpITnkIYgKi8KICAgICAgICB9CgogICAgICAgIGNvbnN0IHdpZHRoID0gTWF0aC5tYXgoMCwgc3RhZ2UuY2xpZW50V2lkdGggLSBob3Jpem9udGFsUGFkZGluZyk7CiAgICAgICAgY29uc3QgaGVpZ2h0ID0gTWF0aC5tYXgoMCwgc3RhZ2UuY2xpZW50SGVpZ2h0IC0gdmVydGljYWxQYWRkaW5nKTsKICAgICAgICBpZiAod2lkdGggPT09IHRoaXMuc3RhZ2VXaWR0aCAmJiBoZWlnaHQgPT09IHRoaXMuc3RhZ2VIZWlnaHQpIHJldHVybjsKICAgICAgICB0aGlzLnN0YWdlV2lkdGggPSB3aWR0aDsKICAgICAgICB0aGlzLnN0YWdlSGVpZ2h0ID0gaGVpZ2h0OwogICAgICB9LAoKICAgICAgZm9jdXNSb290KCkgewogICAgICAgIGNvbnN0IHJvb3QgPSB0aGlzLiRyZWZzLnJvb3Q7CiAgICAgICAgaWYgKCFyb290IHx8IHR5cGVvZiByb290LmZvY3VzICE9PSAnZnVuY3Rpb24nKSByZXR1cm47CiAgICAgICAgdHJ5IHsKICAgICAgICAgIHJvb3QuZm9jdXMoewogICAgICAgICAgICBwcmV2ZW50U2Nyb2xsOiB0cnVlCiAgICAgICAgICB9KTsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgcm9vdC5mb2N1cygpOwogICAgICAgICAgfSBjYXRjaCAoaWdub3JlKSB7CiAgICAgICAgICAgIC8qIOW/veeVpSAqLwogICAgICAgICAgfQogICAgICAgIH0KICAgICAgfSwKCiAgICAgIC8qKgogICAgICAgKiDnvKnmlL7mtbfmiqXjgIJhbmNob3JFdmVudCDmnInlgLzml7bku6XpvKDmoIfkvY3nva7kuLrplJrngrnvvIzlkKbliJnku6XoiJ7lj7DkuK3lv4PkuLrplJrngrnjgIIKICAgICAgICog57yp5pS+5Y+q5pS55Y+YIGlmcmFtZSDnmoQgQ1NTIHRyYW5zZm9ybe+8jOS4jeaUueWPmOW4g+WxgOWwuuWvuO+8jOWboOatpOWvvOWHuuWIhui+qOeOhwogICAgICAgKiDkuI7lvZPliY3nvKnmlL7nuqfliKvml6DlhbPjgIIKICAgICAgICovCiAgICAgIHpvb21UbyhuZXh0Wm9vbSwgYW5jaG9yRXZlbnQpIHsKICAgICAgICBjb25zdCB0YXJnZXQgPSBNYXRoLm1pbigKICAgICAgICAgIHRoaXMubWF4Wm9vbSwKICAgICAgICAgIE1hdGgubWF4KHRoaXMubWluWm9vbSwgTWF0aC5yb3VuZChuZXh0Wm9vbSAqIDEwMCkgLyAxMDApCiAgICAgICAgKTsKICAgICAgICBpZiAoTWF0aC5hYnModGFyZ2V0IC0gdGhpcy56b29tKSA8IDAuMDAxKSByZXR1cm47CgogICAgICAgIGNvbnN0IHN0YWdlID0gdGhpcy4kcmVmcy5zdGFnZTsKICAgICAgICBjb25zdCBmcmFtZSA9IHRoaXMuJHJlZnMucG9zdGVyRnJhbWU7CiAgICAgICAgbGV0IGFuY2hvciA9IG51bGw7CgogICAgICAgIGlmIChzdGFnZSAmJiBmcmFtZSkgewogICAgICAgICAgY29uc3QgcmVjdCA9IGZyYW1lLmdldEJvdW5kaW5nQ2xpZW50UmVjdCgpOwogICAgICAgICAgaWYgKHJlY3Qud2lkdGggPiAwICYmIHJlY3QuaGVpZ2h0ID4gMCkgewogICAgICAgICAgICBjb25zdCBjbGllbnRYID0gYW5jaG9yRXZlbnQgPyBhbmNob3JFdmVudC5jbGllbnRYIDogcmVjdC5sZWZ0ICsgcmVjdC53aWR0aCAvIDI7CiAgICAgICAgICAgIGNvbnN0IGNsaWVudFkgPSBhbmNob3JFdmVudCA/IGFuY2hvckV2ZW50LmNsaWVudFkgOiByZWN0LnRvcCArIHJlY3QuaGVpZ2h0IC8gMjsKICAgICAgICAgICAgYW5jaG9yID0gewogICAgICAgICAgICAgIHg6IChjbGllbnRYIC0gcmVjdC5sZWZ0KSAvIHJlY3Qud2lkdGgsCiAgICAgICAgICAgICAgeTogKGNsaWVudFkgLSByZWN0LnRvcCkgLyByZWN0LmhlaWdodCwKICAgICAgICAgICAgICBjbGllbnRYOiBjbGllbnRYLAogICAgICAgICAgICAgIGNsaWVudFk6IGNsaWVudFkKICAgICAgICAgICAgfTsKICAgICAgICAgIH0KICAgICAgICB9CgogICAgICAgIHRoaXMuem9vbSA9IHRhcmdldDsKCiAgICAgICAgdGhpcy4kbmV4dFRpY2soKCkgPT4gewogICAgICAgICAgY29uc3Qgc3RhZ2VFbCA9IHRoaXMuJHJlZnMuc3RhZ2U7CiAgICAgICAgICBjb25zdCBmcmFtZUVsID0gdGhpcy4kcmVmcy5wb3N0ZXJGcmFtZTsKICAgICAgICAgIGlmICghc3RhZ2VFbCkgcmV0dXJuOwoKICAgICAgICAgIGlmICghYW5jaG9yIHx8ICFmcmFtZUVsIHx8IHRhcmdldCA8PSAxKSB7CiAgICAgICAgICAgIHN0YWdlRWwuc2Nyb2xsTGVmdCA9IDA7CiAgICAgICAgICAgIHN0YWdlRWwuc2Nyb2xsVG9wID0gMDsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQoKICAgICAgICAgIGNvbnN0IHJlY3QgPSBmcmFtZUVsLmdldEJvdW5kaW5nQ2xpZW50UmVjdCgpOwogICAgICAgICAgY29uc3QgZHggPSByZWN0LmxlZnQgKyBhbmNob3IueCAqIHJlY3Qud2lkdGggLSBhbmNob3IuY2xpZW50WDsKICAgICAgICAgIGNvbnN0IGR5ID0gcmVjdC50b3AgKyBhbmNob3IueSAqIHJlY3QuaGVpZ2h0IC0gYW5jaG9yLmNsaWVudFk7CiAgICAgICAgICBzdGFnZUVsLnNjcm9sbExlZnQgKz0gZHg7CiAgICAgICAgICBzdGFnZUVsLnNjcm9sbFRvcCArPSBkeTsKICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIHpvb21JbigpIHsKICAgICAgICB0aGlzLnpvb21Ubyh0aGlzLnpvb20gKyAwLjI1KTsKICAgICAgfSwKCiAgICAgIHpvb21PdXQoKSB7CiAgICAgICAgdGhpcy56b29tVG8odGhpcy56b29tIC0gMC4yNSk7CiAgICAgIH0sCgogICAgICBmaXRQb3N0ZXIoKSB7CiAgICAgICAgdGhpcy56b29tVG8oMSk7CiAgICAgICAgY29uc3Qgc3RhZ2UgPSB0aGlzLiRyZWZzLnN0YWdlOwogICAgICAgIGlmIChzdGFnZSAmJiB0aGlzLnpvb20gPD0gMSkgewogICAgICAgICAgc3RhZ2Uuc2Nyb2xsTGVmdCA9IDA7CiAgICAgICAgICBzdGFnZS5zY3JvbGxUb3AgPSAwOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIGhhbmRsZVdoZWVsKGV2ZW50KSB7CiAgICAgICAgaWYgKCEoZXZlbnQuY3RybEtleSB8fCBldmVudC5tZXRhS2V5KSkgcmV0dXJuOwogICAgICAgIGV2ZW50LnByZXZlbnREZWZhdWx0KCk7CiAgICAgICAgY29uc3QgZmFjdG9yID0gZXZlbnQuZGVsdGFZIDwgMCA/IDEuMTUgOiAwLjg3OwogICAgICAgIHRoaXMuem9vbVRvKHRoaXMuem9vbSAqIGZhY3RvciwgZXZlbnQpOwogICAgICB9LAoKICAgICAgaGFuZGxlU3RhZ2VEb3VibGVDbGljayhldmVudCkgewogICAgICAgIGlmICh0aGlzLmVycm9yRGlhbG9nVmlzaWJsZSkgcmV0dXJuOwogICAgICAgIGlmICh0aGlzLmlzWm9vbWVkKSB7CiAgICAgICAgICB0aGlzLmZpdFBvc3RlcigpOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICB0aGlzLnpvb21UbygyLCBldmVudCk7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgaGFuZGxlUG9pbnRlckRvd24oZXZlbnQpIHsKICAgICAgICB0aGlzLmZvY3VzUm9vdCgpOwogICAgICAgIGlmICghdGhpcy5pc1pvb21lZCB8fCBldmVudC5idXR0b24gIT09IDApIHJldHVybjsKICAgICAgICBjb25zdCBzdGFnZSA9IHRoaXMuJHJlZnMuc3RhZ2U7CiAgICAgICAgaWYgKCFzdGFnZSkgcmV0dXJuOwoKICAgICAgICB0aGlzLnBhblN0YXJ0ID0gewogICAgICAgICAgeDogZXZlbnQuY2xpZW50WCwKICAgICAgICAgIHk6IGV2ZW50LmNsaWVudFksCiAgICAgICAgICBsZWZ0OiBzdGFnZS5zY3JvbGxMZWZ0LAogICAgICAgICAgdG9wOiBzdGFnZS5zY3JvbGxUb3AsCiAgICAgICAgICBwb2ludGVySWQ6IGV2ZW50LnBvaW50ZXJJZAogICAgICAgIH07CiAgICAgICAgdGhpcy5pc1Bhbm5pbmcgPSB0cnVlOwoKICAgICAgICB0cnkgewogICAgICAgICAgc3RhZ2Uuc2V0UG9pbnRlckNhcHR1cmUoZXZlbnQucG9pbnRlcklkKTsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgLyog5b+955Wl77ya5LiN5pSv5oyB5oyH6ZKI5o2V6I635pe25LuN5Y+v6YCa6L+H5rua5Yqo5p2h5bmz56e7ICovCiAgICAgICAgfQogICAgICB9LAoKICAgICAgaGFuZGxlUG9pbnRlck1vdmUoZXZlbnQpIHsKICAgICAgICBpZiAoIXRoaXMuaXNQYW5uaW5nIHx8ICF0aGlzLnBhblN0YXJ0KSByZXR1cm47CiAgICAgICAgY29uc3Qgc3RhZ2UgPSB0aGlzLiRyZWZzLnN0YWdlOwogICAgICAgIGlmICghc3RhZ2UpIHJldHVybjsKICAgICAgICBzdGFnZS5zY3JvbGxMZWZ0ID0gdGhpcy5wYW5TdGFydC5sZWZ0IC0gKGV2ZW50LmNsaWVudFggLSB0aGlzLnBhblN0YXJ0LngpOwogICAgICAgIHN0YWdlLnNjcm9sbFRvcCA9IHRoaXMucGFuU3RhcnQudG9wIC0gKGV2ZW50LmNsaWVudFkgLSB0aGlzLnBhblN0YXJ0LnkpOwogICAgICB9LAoKICAgICAgaGFuZGxlUG9pbnRlckVuZChldmVudCkgewogICAgICAgIGlmICghdGhpcy5pc1Bhbm5pbmcpIHJldHVybjsKICAgICAgICB0aGlzLmlzUGFubmluZyA9IGZhbHNlOwogICAgICAgIGNvbnN0IHN0YWdlID0gdGhpcy4kcmVmcy5zdGFnZTsKICAgICAgICBpZiAoc3RhZ2UgJiYgdGhpcy5wYW5TdGFydCAmJiB0eXBlb2Ygc3RhZ2UucmVsZWFzZVBvaW50ZXJDYXB0dXJlID09PSAnZnVuY3Rpb24nKSB7CiAgICAgICAgICB0cnkgewogICAgICAgICAgICBzdGFnZS5yZWxlYXNlUG9pbnRlckNhcHR1cmUoZXZlbnQucG9pbnRlcklkKTsKICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgIC8qIOW/veeVpSAqLwogICAgICAgICAgfQogICAgICAgIH0KICAgICAgICB0aGlzLnBhblN0YXJ0ID0gbnVsbDsKICAgICAgfSwKCiAgICAgIGhhbmRsZUtleWRvd24oZXZlbnQpIHsKICAgICAgICBpZiAoZXZlbnQua2V5ID09PSAnRXNjYXBlJyB8fCBldmVudC5rZXkgPT09ICdFc2MnIHx8IGV2ZW50LmtleUNvZGUgPT09IDI3KSB7CiAgICAgICAgICBpZiAodGhpcy5lcnJvckRpYWxvZ1Zpc2libGUpIHsKICAgICAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICAgICAgdGhpcy5jbG9zZUVycm9yRGlhbG9nKCk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmICh0aGlzLmVycm9yRGlhbG9nVmlzaWJsZSkgcmV0dXJuOwoKICAgICAgICBjb25zdCB0YXJnZXQgPSBldmVudC50YXJnZXQ7CiAgICAgICAgY29uc3QgdGFnTmFtZSA9IHRhcmdldCAmJiB0YXJnZXQudGFnTmFtZSA/IFN0cmluZyh0YXJnZXQudGFnTmFtZSkudG9Mb3dlckNhc2UoKSA6ICcnOwogICAgICAgIGlmICh0YWdOYW1lID09PSAnaW5wdXQnIHx8IHRhZ05hbWUgPT09ICd0ZXh0YXJlYScgfHwgdGFnTmFtZSA9PT0gJ3NlbGVjdCcpIHJldHVybjsKICAgICAgICBpZiAodGFyZ2V0ICYmIHRhcmdldC5pc0NvbnRlbnRFZGl0YWJsZSkgcmV0dXJuOwogICAgICAgIGlmIChldmVudC5jdHJsS2V5IHx8IGV2ZW50Lm1ldGFLZXkgfHwgZXZlbnQuYWx0S2V5KSByZXR1cm47CgogICAgICAgIGlmIChldmVudC5rZXkgPT09ICcrJyB8fCBldmVudC5rZXkgPT09ICc9JykgewogICAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICAgIHRoaXMuem9vbUluKCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmIChldmVudC5rZXkgPT09ICctJyB8fCBldmVudC5rZXkgPT09ICdfJykgewogICAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICAgIHRoaXMuem9vbU91dCgpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBpZiAoZXZlbnQua2V5ID09PSAnMCcpIHsKICAgICAgICAgIGV2ZW50LnByZXZlbnREZWZhdWx0KCk7CiAgICAgICAgICB0aGlzLmZpdFBvc3RlcigpOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAqIOWvvOWHuiBQTkcgLyBKUEVHCiAgICAgICAqID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBhc3luYyBleHBvcnRJbWFnZShmb3JtYXQpIHsKICAgICAgICBpZiAoIXRoaXMuY2FuRXhwb3J0KSByZXR1cm47CgogICAgICAgIGNvbnN0IGV4cG9ydEZvcm1hdCA9IGZvcm1hdCA9PT0gJ2pwZWcnID8gJ2pwZWcnIDogJ3BuZyc7CiAgICAgICAgdGhpcy5leHBvcnRCdXN5ID0gdHJ1ZTsKICAgICAgICB0aGlzLmV4cG9ydEZvcm1hdCA9IGV4cG9ydEZvcm1hdDsKICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAnJzsKCiAgICAgICAgdHJ5IHsKICAgICAgICAgIC8qIOebtOaOpeaMiSBjYW52YXMg55qEIGJhY2tpbmcgc3RvcmUg5oiq5Zu+77yIcGl4ZWxSYXRpbyAz77yJ77yMCiAgICAgICAgICAgKiDpgb/lhY3mjInpooTop4ggQ1NTIOWwuuWvuOaIquWbvuWQjuWGjeaUvuWkp+WvvOiHtOaWh+Wtl+WPkeiZmuOAgiAqLwogICAgICAgICAgY29uc3QgY2FwdHVyZSA9IGF3YWl0IHRoaXMucG9zdENvbW1hbmQoJ3NjcmVlbnNob3QnLCB7CiAgICAgICAgICAgIG1pbWVUeXBlOiAnaW1hZ2UvcG5nJywKICAgICAgICAgICAgcXVhbGl0eTogMSwKICAgICAgICAgICAgbmF0aXZlOiB0cnVlCiAgICAgICAgICB9LCA2MDAwMCk7CgogICAgICAgICAgaWYgKCFjYXB0dXJlIHx8ICFjYXB0dXJlLmRhdGFVcmwpIHRocm93IG5ldyBFcnJvcign5oiq5Zu+5pWw5o2u5Li656m6Jyk7CgogICAgICAgICAgY29uc3QgaW1hZ2UgPSBhd2FpdCB0aGlzLmNvbXBvc2VQb3N0ZXJJbWFnZShjYXB0dXJlLmRhdGFVcmwsIGV4cG9ydEZvcm1hdCk7CiAgICAgICAgICBjb25zdCBmaWxlbmFtZSA9IHRoaXMuYnVpbGRUaXRsZUZpbGVOYW1lKGV4cG9ydEZvcm1hdCA9PT0gJ2pwZWcnID8gJ2pwZycgOiAncG5nJyk7CgogICAgICAgICAgdGhpcy5kb3dubG9hZFVybChpbWFnZS5kYXRhVXJsLCBmaWxlbmFtZSk7CgogICAgICAgICAgdGhpcy5sYXN0RXhwb3J0ID0gewogICAgICAgICAgICB1cmw6IGltYWdlLmRhdGFVcmwsCiAgICAgICAgICAgIGZpbGVuYW1lOiBmaWxlbmFtZSwKICAgICAgICAgICAgZm9ybWF0OiBleHBvcnRGb3JtYXQsCiAgICAgICAgICAgIGZvcm1hdExhYmVsOiBleHBvcnRGb3JtYXQgPT09ICdqcGVnJyA/ICdKUEVHJyA6ICdQTkcnLAogICAgICAgICAgICBtaW1lVHlwZTogaW1hZ2UubWltZVR5cGUsCiAgICAgICAgICAgIHdpZHRoOiBpbWFnZS53aWR0aCwKICAgICAgICAgICAgaGVpZ2h0OiBpbWFnZS5oZWlnaHQsCiAgICAgICAgICAgIHNpemU6IGltYWdlLnNpemUsCiAgICAgICAgICAgIHNpemVMYWJlbDogdGhpcy5mb3JtYXRGaWxlU2l6ZShpbWFnZS5zaXplKQogICAgICAgICAgfTsKCiAgICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAoZXhwb3J0Rm9ybWF0ID09PSAnanBlZycgPyAnSlBFRycgOiAnUE5HJykgKwogICAgICAgICAgICAnIOW3suWvvOWHuu+8iCcgKyBpbWFnZS53aWR0aCArICcgw5cgJyArIGltYWdlLmhlaWdodCArICfvvIknOwoKICAgICAgICAgIHRoaXMuJGVtaXQoJ2V4cG9ydC1pbWFnZScsIHsKICAgICAgICAgICAgZGF0YVVybDogaW1hZ2UuZGF0YVVybCwKICAgICAgICAgICAgZm9ybWF0OiBleHBvcnRGb3JtYXQsCiAgICAgICAgICAgIG1pbWVUeXBlOiBpbWFnZS5taW1lVHlwZSwKICAgICAgICAgICAgd2lkdGg6IGltYWdlLndpZHRoLAogICAgICAgICAgICBoZWlnaHQ6IGltYWdlLmhlaWdodCwKICAgICAgICAgICAgc2l6ZTogaW1hZ2Uuc2l6ZSwKICAgICAgICAgICAgZmlsZW5hbWU6IGZpbGVuYW1lCiAgICAgICAgICB9KTsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJ+WvvOWHuuWksei0pe+8micgKyB0aGlzLmVycm9yTWVzc2FnZShlcnJvcik7CiAgICAgICAgICBjb25zb2xlLndhcm4oJ1tMZWFmZXJKUyBQb3N0ZXJdIGV4cG9ydCBmYWlsZWQ6JywgZXJyb3IpOwogICAgICAgICAgdGhpcy4kZW1pdCgnY2FwdHVyZS1lcnJvcicsIHsKICAgICAgICAgICAgYWN0aW9uOiAnZXhwb3J0SW1hZ2UnLAogICAgICAgICAgICBlcnJvcjogZXJyb3IKICAgICAgICAgIH0pOwogICAgICAgIH0gZmluYWxseSB7CiAgICAgICAgICB0aGlzLmV4cG9ydEJ1c3kgPSBmYWxzZTsKICAgICAgICAgIHRoaXMuZXhwb3J0Rm9ybWF0ID0gJyc7CiAgICAgICAgICB0aGlzLnNjaGVkdWxlU3RhdHVzUmVzZXQoNTAwMCk7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgLyoqCiAgICAgICAqIOaKiuaIquWbvuWQiOaIkOS4uua1t+aKpeavlOS+i+eahOi+k+WHuuWbvu+8mgogICAgICAgKiAgIC0g5q+U5L6L5Y+WIGFjY291bnREYXRhLndpZHRoIC8gaGVpZ2h077yM5LiN5YaNIGNvdmVyIOijgeWIh+aIkCAzOjTvvJsKICAgICAgICogICAtIOefrei+uSAyNzAwcHjvvIjkuI7mj5DnpLror40gOTAwIMOXIDMg5a+56b2Q77yJ77yM6ZW/6L655oyJ5q+U5L6L77ybCiAgICAgICAqICAgLSBKUEVHIOWFiOmTuua3seiJsuiDjOaZr++8jOmBv+WFjemAj+aYjuWMuuWfn+WcqOWvvOWHuuWQjuWPmOm7keaIluWPmOeZveOAggogICAgICAgKi8KICAgICAgYXN5bmMgY29tcG9zZVBvc3RlckltYWdlKGRhdGFVcmwsIGZvcm1hdCkgewogICAgICAgIGNvbnN0IGltYWdlID0gYXdhaXQgdGhpcy5sb2FkSW1hZ2UoZGF0YVVybCk7CiAgICAgICAgY29uc3Qgc291cmNlV2lkdGggPSBpbWFnZS5uYXR1cmFsV2lkdGggfHwgaW1hZ2Uud2lkdGg7CiAgICAgICAgY29uc3Qgc291cmNlSGVpZ2h0ID0gaW1hZ2UubmF0dXJhbEhlaWdodCB8fCBpbWFnZS5oZWlnaHQ7CiAgICAgICAgaWYgKCFzb3VyY2VXaWR0aCB8fCAhc291cmNlSGVpZ2h0KSB0aHJvdyBuZXcgRXJyb3IoJ+aIquWbvuWbvuWDj+WwuuWvuOaXoOaViCcpOwoKICAgICAgICBjb25zdCB0YXJnZXRXaWR0aCA9IHRoaXMuZXhwb3J0V2lkdGg7CiAgICAgICAgY29uc3QgdGFyZ2V0SGVpZ2h0ID0gdGhpcy5leHBvcnRIZWlnaHQ7CgogICAgICAgIGNvbnN0IGNhbnZhcyA9IGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ2NhbnZhcycpOwogICAgICAgIGNhbnZhcy53aWR0aCA9IHRhcmdldFdpZHRoOwogICAgICAgIGNhbnZhcy5oZWlnaHQgPSB0YXJnZXRIZWlnaHQ7CgogICAgICAgIGNvbnN0IGNvbnRleHQgPSBjYW52YXMuZ2V0Q29udGV4dCgnMmQnKTsKICAgICAgICBpZiAoIWNvbnRleHQpIHRocm93IG5ldyBFcnJvcign5peg5rOV5Yib5bu65a+85Ye655So55qEIENhbnZhcycpOwoKICAgICAgICBpZiAoZm9ybWF0ID09PSAnanBlZycpIHsKICAgICAgICAgIGNvbnRleHQuZmlsbFN0eWxlID0gJyMwNDA4MTAnOwogICAgICAgICAgY29udGV4dC5maWxsUmVjdCgwLCAwLCB0YXJnZXRXaWR0aCwgdGFyZ2V0SGVpZ2h0KTsKICAgICAgICB9CgogICAgICAgIGNvbnRleHQuaW1hZ2VTbW9vdGhpbmdFbmFibGVkID0gdHJ1ZTsKICAgICAgICBjb250ZXh0LmltYWdlU21vb3RoaW5nUXVhbGl0eSA9ICdoaWdoJzsKCiAgICAgICAgY29udGV4dC5kcmF3SW1hZ2UoaW1hZ2UsIDAsIDAsIHRhcmdldFdpZHRoLCB0YXJnZXRIZWlnaHQpOwoKICAgICAgICBjb25zdCBtaW1lVHlwZSA9IGZvcm1hdCA9PT0gJ2pwZWcnID8gJ2ltYWdlL2pwZWcnIDogJ2ltYWdlL3BuZyc7CiAgICAgICAgY29uc3Qgb3V0cHV0ID0gY2FudmFzLnRvRGF0YVVSTChtaW1lVHlwZSwgZm9ybWF0ID09PSAnanBlZycgPyAwLjkyIDogMSk7CgogICAgICAgIHJldHVybiB7CiAgICAgICAgICBkYXRhVXJsOiBvdXRwdXQsCiAgICAgICAgICBtaW1lVHlwZTogbWltZVR5cGUsCiAgICAgICAgICB3aWR0aDogdGFyZ2V0V2lkdGgsCiAgICAgICAgICBoZWlnaHQ6IHRhcmdldEhlaWdodCwKICAgICAgICAgIHNpemU6IHRoaXMuZGF0YVVybEJ5dGVTaXplKG91dHB1dCkKICAgICAgICB9OwogICAgICB9LAoKICAgICAgbG9hZEltYWdlKGRhdGFVcmwpIHsKICAgICAgICByZXR1cm4gbmV3IFByb21pc2UoKHJlc29sdmUsIHJlamVjdCkgPT4gewogICAgICAgICAgaWYgKHR5cGVvZiBJbWFnZSA9PT0gJ3VuZGVmaW5lZCcpIHsKICAgICAgICAgICAgcmVqZWN0KG5ldyBFcnJvcign5b2T5YmN546v5aKD5LiN5pSv5oyB5Zu+54mH5a+85Ye6JykpOwogICAgICAgICAgICByZXR1cm47CiAgICAgICAgICB9CiAgICAgICAgICBjb25zdCBpbWFnZSA9IG5ldyBJbWFnZSgpOwogICAgICAgICAgaW1hZ2Uub25sb2FkID0gKCkgPT4gcmVzb2x2ZShpbWFnZSk7CiAgICAgICAgICBpbWFnZS5vbmVycm9yID0gKCkgPT4gcmVqZWN0KG5ldyBFcnJvcign5peg5rOV6K+75Y+W5oiq5Zu+5Zu+5YOPJykpOwogICAgICAgICAgaW1hZ2Uuc3JjID0gZGF0YVVybDsKICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIGRhdGFVcmxCeXRlU2l6ZShkYXRhVXJsKSB7CiAgICAgICAgY29uc3QgY29tbWFJbmRleCA9IGRhdGFVcmwuaW5kZXhPZignLCcpOwogICAgICAgIGlmIChjb21tYUluZGV4ID09PSAtMSkgcmV0dXJuIGRhdGFVcmwubGVuZ3RoOwogICAgICAgIGNvbnN0IGJhc2U2NCA9IGRhdGFVcmwuc2xpY2UoY29tbWFJbmRleCArIDEpOwogICAgICAgIGxldCBwYWRkaW5nID0gMDsKICAgICAgICBpZiAoYmFzZTY0LmNoYXJBdChiYXNlNjQubGVuZ3RoIC0gMSkgPT09ICc9JykgcGFkZGluZyArPSAxOwogICAgICAgIGlmIChiYXNlNjQuY2hhckF0KGJhc2U2NC5sZW5ndGggLSAyKSA9PT0gJz0nKSBwYWRkaW5nICs9IDE7CiAgICAgICAgcmV0dXJuIE1hdGgubWF4KDAsIE1hdGguZmxvb3IoYmFzZTY0Lmxlbmd0aCAqIDMgLyA0KSAtIHBhZGRpbmcpOwogICAgICB9LAoKICAgICAgZm9ybWF0RmlsZVNpemUoYnl0ZXMpIHsKICAgICAgICBjb25zdCBzaXplID0gTnVtYmVyKGJ5dGVzKSB8fCAwOwogICAgICAgIGlmIChzaXplIDwgMTAyNCkgcmV0dXJuIHNpemUgKyAnIEInOwogICAgICAgIGlmIChzaXplIDwgMTAyNCAqIDEwMjQpIHJldHVybiAoc2l6ZSAvIDEwMjQpLnRvRml4ZWQoMSkgKyAnIEtCJzsKICAgICAgICByZXR1cm4gKHNpemUgLyAoMTAyNCAqIDEwMjQpKS50b0ZpeGVkKDIpICsgJyBNQic7CiAgICAgIH0sCiAgICB9CiAgfTsKPC9zY3JpcHQ+CjxzdHlsZSBzY29wZWQ+CiAgLnBvc3Rlci12aWV3ZXIgewogICAgZGlzcGxheTogZmxleDsKICAgIGZsZXgtZGlyZWN0aW9uOiBjb2x1bW47CiAgICB3aWR0aDogMTAwJTsKICAgIGhlaWdodDogMTAwJTsKICAgIG1pbi1oZWlnaHQ6IDA7CiAgICBwYWRkaW5nOiAxNnB4IDE4cHggMTRweDsKICAgIGJveC1zaXppbmc6IGJvcmRlci1ib3g7CiAgICBvdmVyZmxvdzogaGlkZGVuOwogICAgYm9yZGVyOiAxcHggc29saWQgI2U2ZWJmMjsKICAgIGJvcmRlci1yYWRpdXM6IDE2cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogICAgYm94LXNoYWRvdzogMCAxcHggMnB4IHJnYmEoMTUsIDIzLCA0MiwgMC4wNCksIDAgMThweCA0NHB4IC0yNHB4IHJnYmEoMTUsIDIzLCA0MiwgMC4yOCk7CiAgICBjb2xvcjogIzBmMTcyYTsKICAgIGZvbnQtZmFtaWx5OiBzeXN0ZW0tdWksIC1hcHBsZS1zeXN0ZW0sICJQaW5nRmFuZyBTQyIsICJNaWNyb3NvZnQgWWFIZWkiLCAiSGVsdmV0aWNhIE5ldWUiLCBBcmlhbCwgc2Fucy1zZXJpZjsKICAgIG91dGxpbmU6IG5vbmU7CiAgfQoKICAucG9zdGVyLXZpZXdlcjpmb2N1cy12aXNpYmxlIHsKICAgIGJveC1zaGFkb3c6IDAgMCAwIDNweCByZ2JhKDU5LCAxMzAsIDI0NiwgMC4xOCksIDAgMThweCA0NHB4IC0yNHB4IHJnYmEoMTUsIDIzLCA0MiwgMC4yOCk7CiAgfQoKICAvKiAtLS0tLS0tLS0tIOmhtumDqOagh+mimCAtLS0tLS0tLS0tICovCiAgLnBvc3Rlci1oZWFkZXIgewogICAgZGlzcGxheTogZmxleDsKICAgIGZsZXgtd3JhcDogd3JhcDsKICAgIGFsaWduLWl0ZW1zOiBmbGV4LXN0YXJ0OwogICAganVzdGlmeS1jb250ZW50OiBzcGFjZS1iZXR3ZWVuOwogICAgZ2FwOiAxMnB4OwogICAgZmxleC1zaHJpbms6IDA7CiAgICBwYWRkaW5nLWJvdHRvbTogMTJweDsKICAgIGJvcmRlci1ib3R0b206IDFweCBzb2xpZCAjZWVmMmY3OwogIH0KCiAgLnBvc3Rlci1oZWFkaW5nIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBmbGV4LWRpcmVjdGlvbjogY29sdW1uOwogICAgZ2FwOiA1cHg7CiAgICBtaW4td2lkdGg6IDA7CiAgfQoKICAucG9zdGVyLWhlYWRpbmctbGluZSB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgZmxleC13cmFwOiB3cmFwOwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogOXB4OwogICAgbWluLXdpZHRoOiAwOwogIH0KCiAgLnBvc3Rlci10aXRsZSB7CiAgICBtYXJnaW46IDA7CiAgICBjb2xvcjogIzBmMTcyYTsKICAgIGZvbnQtc2l6ZTogMThweDsKICAgIGZvbnQtd2VpZ2h0OiA2NTA7CiAgICBsaW5lLWhlaWdodDogMS4zNTsKICAgIGxldHRlci1zcGFjaW5nOiAtMC4wMWVtOwogIH0KCiAgLnBvc3Rlci1yYXRpbyB7CiAgICBmbGV4LXNocmluazogMDsKICAgIHBhZGRpbmc6IDJweCA3cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDE0LCAxNjUsIDIzMywgMC4yMik7CiAgICBib3JkZXItcmFkaXVzOiA2cHg7CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDE0LCAxNjUsIDIzMywgMC4wOCk7CiAgICBjb2xvcjogIzAyODRjNzsKICAgIGZvbnQtZmFtaWx5OiB1aS1tb25vc3BhY2UsIFNGTW9uby1SZWd1bGFyLCBNZW5sbywgTW9uYWNvLCBDb25zb2xhcywgbW9ub3NwYWNlOwogICAgZm9udC1zaXplOiAxMHB4OwogICAgZm9udC13ZWlnaHQ6IDYwMDsKICAgIGxpbmUtaGVpZ2h0OiAxLjU7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wNGVtOwogIH0KCiAgLnBvc3Rlci1kZXNjIHsKICAgIG1hcmdpbjogMDsKICAgIGNvbG9yOiAjNjQ3NDhiOwogICAgZm9udC1zaXplOiAxMnB4OwogICAgbGluZS1oZWlnaHQ6IDEuNjU7CiAgICBvdmVyZmxvdy13cmFwOiBhbnl3aGVyZTsKICB9CgogIC5wb3N0ZXItc3RhdHVzYmFyIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBmbGV4LXdyYXA6IHdyYXA7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBmbGV4LWVuZDsKICAgIGdhcDogOHB4OwogICAgbWluLWhlaWdodDogMjhweDsKICB9CgogIC5wb3N0ZXItc3RhdHVzIHsKICAgIGRpc3BsYXk6IGlubGluZS1mbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogNnB4OwogICAgaGVpZ2h0OiAyNnB4OwogICAgcGFkZGluZzogMCAxMHB4OwogICAgYm9yZGVyOiAxcHggc29saWQgI2UyZThmMDsKICAgIGJvcmRlci1yYWRpdXM6IDk5OXB4OwogICAgYmFja2dyb3VuZDogI2Y4ZmFmYzsKICAgIGNvbG9yOiAjNDc1NTY5OwogICAgZm9udC1zaXplOiAxMXB4OwogICAgbGluZS1oZWlnaHQ6IDE7CiAgICB3aGl0ZS1zcGFjZTogbm93cmFwOwogIH0KCiAgLnBvc3Rlci1zdGF0dXMtZG90IHsKICAgIHdpZHRoOiA2cHg7CiAgICBoZWlnaHQ6IDZweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgYm9yZGVyLXJhZGl1czogOTk5cHg7CiAgICBiYWNrZ3JvdW5kOiAjOTRhM2I4OwogIH0KCiAgLnBvc3Rlci1zdGF0dXMuaXMtbG9hZGluZyAucG9zdGVyLXN0YXR1cy1kb3QgewogICAgYmFja2dyb3VuZDogI2Y1OWUwYjsKICAgIGJveC1zaGFkb3c6IDAgMCAwIDNweCByZ2JhKDI0NSwgMTU4LCAxMSwgMC4xNCk7CiAgfQoKICAucG9zdGVyLXN0YXR1cy5pcy1yZWFkeSAucG9zdGVyLXN0YXR1cy1kb3QgewogICAgYmFja2dyb3VuZDogIzEwYjk4MTsKICAgIGJveC1zaGFkb3c6IDAgMCAwIDNweCByZ2JhKDE2LCAxODUsIDEyOSwgMC4xNCk7CiAgfQoKICAucG9zdGVyLXN0YXR1cy5pcy1idXN5IC5wb3N0ZXItc3RhdHVzLWRvdCB7CiAgICBiYWNrZ3JvdW5kOiAjMGVhNWU5OwogICAgYm94LXNoYWRvdzogMCAwIDAgM3B4IHJnYmEoMTQsIDE2NSwgMjMzLCAwLjE0KTsKICAgIGFuaW1hdGlvbjogcG9zdGVyLXB1bHNlIDEuMXMgZWFzZS1pbi1vdXQgaW5maW5pdGU7CiAgfQoKICAucG9zdGVyLXN0YXR1cy5pcy1lcnJvciB7CiAgICBib3JkZXItY29sb3I6IHJnYmEoMjM5LCA2OCwgNjgsIDAuMjQpOwogICAgYmFja2dyb3VuZDogcmdiYSgyMzksIDY4LCA2OCwgMC4wNik7CiAgICBjb2xvcjogI2I5MWMxYzsKICB9CgogIC5wb3N0ZXItc3RhdHVzLmlzLWVycm9yIC5wb3N0ZXItc3RhdHVzLWRvdCB7CiAgICBiYWNrZ3JvdW5kOiAjZWY0NDQ0OwogICAgYm94LXNoYWRvdzogMCAwIDAgM3B4IHJnYmEoMjM5LCA2OCwgNjgsIDAuMTQpOwogIH0KCiAgQGtleWZyYW1lcyBwb3N0ZXItcHVsc2UgewoKICAgIDAlLAogICAgMTAwJSB7CiAgICAgIG9wYWNpdHk6IDE7CiAgICB9CgogICAgNTAlIHsKICAgICAgb3BhY2l0eTogMC40NTsKICAgIH0KICB9CgogIC5wb3N0ZXItZXJyb3ItY2hpcCB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBnYXA6IDRweDsKICAgIGhlaWdodDogMjZweDsKICAgIHBhZGRpbmc6IDAgOXB4OwogICAgYm9yZGVyOiAxcHggc29saWQgcmdiYSgyMzksIDY4LCA2OCwgMC4yOCk7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGJhY2tncm91bmQ6ICNmZWYyZjI7CiAgICBjb2xvcjogI2I5MWMxYzsKICAgIGZvbnQtc2l6ZTogMTFweDsKICAgIGxpbmUtaGVpZ2h0OiAxOwogICAgY3Vyc29yOiBwb2ludGVyOwogICAgdHJhbnNpdGlvbjogYmFja2dyb3VuZC1jb2xvciAwLjE1cyBlYXNlLCBib3JkZXItY29sb3IgMC4xNXMgZWFzZTsKICB9CgogIC5wb3N0ZXItZXJyb3ItY2hpcDpob3ZlciB7CiAgICBib3JkZXItY29sb3I6IHJnYmEoMjIwLCAzOCwgMzgsIDAuNSk7CiAgICBiYWNrZ3JvdW5kOiAjZmVlMmUyOwogIH0KCiAgLyogLS0tLS0tLS0tLSDlt6XlhbfmoI8gLS0tLS0tLS0tLSAqLwogIC5wb3N0ZXItdG9vbGJhciB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgZmxleC13cmFwOiB3cmFwOwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGp1c3RpZnktY29udGVudDogc3BhY2UtYmV0d2VlbjsKICAgIGdhcDogMTBweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgbWFyZ2luOiAxMHB4IDA7CiAgICBwYWRkaW5nOiA4cHggMTBweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkICNlZWYyZjc7CiAgICBib3JkZXItcmFkaXVzOiAxMnB4OwogICAgYmFja2dyb3VuZDogI2Y4ZmFmYzsKICB9CgogIC5wb3N0ZXItdG9vbGdyb3VwIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiA0cHg7CiAgICBtaW4taGVpZ2h0OiAzMnB4OwogIH0KCiAgLnBvc3Rlci1pY29uIHsKICAgIHdpZHRoOiAxNXB4OwogICAgaGVpZ2h0OiAxNXB4OwogICAgZmxleC1zaHJpbms6IDA7CiAgfQoKICAucG9zdGVyLWljb24tYnRuLAogIC5wb3N0ZXItdGV4dC1idG4sCiAgLnBvc3Rlci16b29tLXZhbHVlIHsKICAgIGRpc3BsYXk6IGlubGluZS1mbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGp1c3RpZnktY29udGVudDogY2VudGVyOwogICAgaGVpZ2h0OiAzMHB4OwogICAgYm9yZGVyOiAxcHggc29saWQgdHJhbnNwYXJlbnQ7CiAgICBib3JkZXItcmFkaXVzOiA4cHg7CiAgICBiYWNrZ3JvdW5kOiB0cmFuc3BhcmVudDsKICAgIGNvbG9yOiAjMzM0MTU1OwogICAgZm9udC1zaXplOiAxMXB4OwogICAgbGluZS1oZWlnaHQ6IDE7CiAgICBjdXJzb3I6IHBvaW50ZXI7CiAgICB0cmFuc2l0aW9uOiBiYWNrZ3JvdW5kLWNvbG9yIDAuMTVzIGVhc2UsIGJvcmRlci1jb2xvciAwLjE1cyBlYXNlLCBjb2xvciAwLjE1cyBlYXNlOwogIH0KCiAgLnBvc3Rlci1pY29uLWJ0biB7CiAgICB3aWR0aDogMzBweDsKICAgIHBhZGRpbmc6IDA7CiAgfQoKICAucG9zdGVyLXRleHQtYnRuIHsKICAgIHBhZGRpbmc6IDAgMTBweDsKICAgIGZvbnQtd2VpZ2h0OiA1MDA7CiAgfQoKICAucG9zdGVyLXpvb20tdmFsdWUgewogICAgbWluLXdpZHRoOiA1OHB4OwogICAgcGFkZGluZzogMCA2cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogICAgYm9yZGVyLWNvbG9yOiAjZTJlOGYwOwogICAgY29sb3I6ICMwZjE3MmE7CiAgICBmb250LWZhbWlseTogdWktbW9ub3NwYWNlLCBTRk1vbm8tUmVndWxhciwgTWVubG8sIE1vbmFjbywgQ29uc29sYXMsIG1vbm9zcGFjZTsKICAgIGZvbnQtc2l6ZTogMTFweDsKICAgIGZvbnQtd2VpZ2h0OiA2MDA7CiAgICBsZXR0ZXItc3BhY2luZzogLTAuMDJlbTsKICB9CgogIC5wb3N0ZXItem9vbS12YWx1ZSBzcGFuIHsKICAgIG1hcmdpbi1sZWZ0OiAxcHg7CiAgICBjb2xvcjogIzk0YTNiODsKICAgIGZvbnQtc2l6ZTogOXB4OwogICAgZm9udC13ZWlnaHQ6IDUwMDsKICB9CgogIC5wb3N0ZXItaWNvbi1idG46aG92ZXI6bm90KDpkaXNhYmxlZCksCiAgLnBvc3Rlci10ZXh0LWJ0bjpob3Zlcjpub3QoOmRpc2FibGVkKSB7CiAgICBiYWNrZ3JvdW5kOiAjZThlZWY2OwogICAgY29sb3I6ICMwZjE3MmE7CiAgfQoKICAucG9zdGVyLXpvb20tdmFsdWU6aG92ZXIgewogICAgYm9yZGVyLWNvbG9yOiAjY2JkNWUxOwogICAgYmFja2dyb3VuZDogI2ZmZmZmZjsKICB9CgogIC5wb3N0ZXItaWNvbi1idG46ZGlzYWJsZWQsCiAgLnBvc3Rlci10ZXh0LWJ0bjpkaXNhYmxlZCwKICAucG9zdGVyLWZvcm1hdC1idG46ZGlzYWJsZWQgewogICAgb3BhY2l0eTogMC4zODsKICAgIGN1cnNvcjogbm90LWFsbG93ZWQ7CiAgfQoKICAucG9zdGVyLXRvb2wtZGl2aWRlciB7CiAgICB3aWR0aDogMXB4OwogICAgaGVpZ2h0OiAxNnB4OwogICAgbWFyZ2luOiAwIDRweDsKICAgIGJhY2tncm91bmQ6ICNlMmU4ZjA7CiAgfQoKICAucG9zdGVyLWV4cG9ydGdyb3VwIHsKICAgIGdhcDogNnB4OwogIH0KCiAgLnBvc3Rlci1leHBvcnQtbGFiZWwgewogICAgbWFyZ2luLXJpZ2h0OiAycHg7CiAgICBjb2xvcjogIzk0YTNiODsKICAgIGZvbnQtc2l6ZTogMTFweDsKICAgIHVzZXItc2VsZWN0OiBub25lOwogIH0KCiAgLnBvc3Rlci1mb3JtYXQtYnRuIHsKICAgIGRpc3BsYXk6IGlubGluZS1mbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGp1c3RpZnktY29udGVudDogY2VudGVyOwogICAgZ2FwOiA2cHg7CiAgICBtaW4td2lkdGg6IDUycHg7CiAgICBoZWlnaHQ6IDMwcHg7CiAgICBwYWRkaW5nOiAwIDEycHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCAjY2JkNWUxOwogICAgYm9yZGVyLXJhZGl1czogOHB4OwogICAgYmFja2dyb3VuZDogI2ZmZmZmZjsKICAgIGNvbG9yOiAjMzM0MTU1OwogICAgZm9udC1mYW1pbHk6IHVpLW1vbm9zcGFjZSwgU0ZNb25vLVJlZ3VsYXIsIE1lbmxvLCBNb25hY28sIENvbnNvbGFzLCBtb25vc3BhY2U7CiAgICBmb250LXNpemU6IDExcHg7CiAgICBmb250LXdlaWdodDogNjAwOwogICAgbGluZS1oZWlnaHQ6IDE7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wMmVtOwogICAgY3Vyc29yOiBwb2ludGVyOwogICAgdHJhbnNpdGlvbjogYmFja2dyb3VuZC1jb2xvciAwLjE1cyBlYXNlLCBib3JkZXItY29sb3IgMC4xNXMgZWFzZSwgY29sb3IgMC4xNXMgZWFzZSwgYm94LXNoYWRvdyAwLjE1cyBlYXNlOwogIH0KCiAgLnBvc3Rlci1mb3JtYXQtYnRuOmhvdmVyOm5vdCg6ZGlzYWJsZWQpIHsKICAgIGJvcmRlci1jb2xvcjogIzM4YmRmODsKICAgIGJhY2tncm91bmQ6ICNmMGY5ZmY7CiAgICBjb2xvcjogIzAzNjlhMTsKICB9CgogIC5wb3N0ZXItZm9ybWF0LWJ0bi0tanBlZzpob3Zlcjpub3QoOmRpc2FibGVkKSB7CiAgICBib3JkZXItY29sb3I6ICNhNzhiZmE7CiAgICBiYWNrZ3JvdW5kOiAjZjVmM2ZmOwogICAgY29sb3I6ICM2ZDI4ZDk7CiAgfQoKICAucG9zdGVyLWRvd25sb2FkLWNoaXAgewogICAgZGlzcGxheTogaW5saW5lLWZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiA0cHg7CiAgICBoZWlnaHQ6IDMwcHg7CiAgICBtYXJnaW4tbGVmdDogMnB4OwogICAgcGFkZGluZzogMCAxMHB4OwogICAgYm9yZGVyOiAxcHggc29saWQgcmdiYSgxNiwgMTg1LCAxMjksIDAuMjgpOwogICAgYm9yZGVyLXJhZGl1czogOHB4OwogICAgYmFja2dyb3VuZDogcmdiYSgxNiwgMTg1LCAxMjksIDAuMDgpOwogICAgY29sb3I6ICMwNDc4NTc7CiAgICBmb250LXNpemU6IDExcHg7CiAgICBsaW5lLWhlaWdodDogMTsKICAgIHRleHQtZGVjb3JhdGlvbjogbm9uZTsKICAgIHdoaXRlLXNwYWNlOiBub3dyYXA7CiAgICB0cmFuc2l0aW9uOiBiYWNrZ3JvdW5kLWNvbG9yIDAuMTVzIGVhc2UsIGJvcmRlci1jb2xvciAwLjE1cyBlYXNlOwogIH0KCiAgLnBvc3Rlci1kb3dubG9hZC1jaGlwOmhvdmVyIHsKICAgIGJvcmRlci1jb2xvcjogcmdiYSg1LCAxNTAsIDEwNSwgMC41KTsKICAgIGJhY2tncm91bmQ6IHJnYmEoMTYsIDE4NSwgMTI5LCAwLjE0KTsKICB9CgogIC5wb3N0ZXItZG93bmxvYWQtY2hpcCAucG9zdGVyLWljb24gewogICAgd2lkdGg6IDEzcHg7CiAgICBoZWlnaHQ6IDEzcHg7CiAgfQoKICAvKiAtLS0tLS0tLS0tIOa1t+aKpeiInuWPsCAtLS0tLS0tLS0tICovCiAgLnBvc3Rlci1zdGFnZSB7CiAgICBwb3NpdGlvbjogcmVsYXRpdmU7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgZmxleDogMSAxIGF1dG87CiAgICBtaW4taGVpZ2h0OiAwOwogICAgcGFkZGluZzogMjhweDsKICAgIGJveC1zaXppbmc6IGJvcmRlci1ib3g7CiAgICBvdmVyZmxvdzogYXV0bzsKICAgIG92ZXJzY3JvbGwtYmVoYXZpb3I6IGNvbnRhaW47CiAgICBib3JkZXI6IDFweCBzb2xpZCAjMWUyOTNiOwogICAgYm9yZGVyLXJhZGl1czogMTJweDsKICAgIGJhY2tncm91bmQtY29sb3I6ICMwYjEyMjA7CiAgICBiYWNrZ3JvdW5kLWltYWdlOgogICAgICByYWRpYWwtZ3JhZGllbnQocmdiYSgxNDgsIDE2MywgMTg0LCAwLjE2KSAxcHgsIHRyYW5zcGFyZW50IDFweCksCiAgICAgIHJhZGlhbC1ncmFkaWVudChlbGxpcHNlIGF0IDUwJSAwJSwgcmdiYSg1NiwgMTg5LCAyNDgsIDAuMDcpLCB0cmFuc3BhcmVudCA2MiUpOwogICAgYmFja2dyb3VuZC1zaXplOiAxOHB4IDE4cHgsIDEwMCUgMTAwJTsKICAgIGJveC1zaGFkb3c6IGluc2V0IDAgMXB4IDAgcmdiYSgyNTUsIDI1NSwgMjU1LCAwLjA0KSwgaW5zZXQgMCAwIDYwcHggcmdiYSgyLCA2LCAyMywgMC41NSk7CiAgICB1c2VyLXNlbGVjdDogbm9uZTsKICAgIC13ZWJraXQtdXNlci1zZWxlY3Q6IG5vbmU7CiAgICBzY3JvbGxiYXItd2lkdGg6IHRoaW47CiAgICBzY3JvbGxiYXItY29sb3I6IHJnYmEoMTQ4LCAxNjMsIDE4NCwgMC4zOCkgdHJhbnNwYXJlbnQ7CiAgfQoKICAucG9zdGVyLXN0YWdlOjotd2Via2l0LXNjcm9sbGJhciB7CiAgICB3aWR0aDogMTBweDsKICAgIGhlaWdodDogMTBweDsKICB9CgogIC5wb3N0ZXItc3RhZ2U6Oi13ZWJraXQtc2Nyb2xsYmFyLXRyYWNrIHsKICAgIGJhY2tncm91bmQ6IHRyYW5zcGFyZW50OwogIH0KCiAgLnBvc3Rlci1zdGFnZTo6LXdlYmtpdC1zY3JvbGxiYXItdGh1bWIgewogICAgYm9yZGVyOiAycHggc29saWQgdHJhbnNwYXJlbnQ7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGJhY2tncm91bmQ6IHJnYmEoMTQ4LCAxNjMsIDE4NCwgMC4zOCk7CiAgICBiYWNrZ3JvdW5kLWNsaXA6IHBhZGRpbmctYm94OwogIH0KCiAgLnBvc3Rlci1zdGFnZTo6LXdlYmtpdC1zY3JvbGxiYXItdGh1bWI6aG92ZXIgewogICAgYmFja2dyb3VuZDogcmdiYSgxNDgsIDE2MywgMTg0LCAwLjU4KTsKICAgIGJhY2tncm91bmQtY2xpcDogcGFkZGluZy1ib3g7CiAgfQoKICAucG9zdGVyLXNwYWNlIHsKICAgIHBvc2l0aW9uOiByZWxhdGl2ZTsKICAgIGZsZXg6IDAgMCBhdXRvOwogICAgbWFyZ2luOiBhdXRvOwogIH0KCiAgLnBvc3Rlci1mcmFtZSB7CiAgICBwb3NpdGlvbjogYWJzb2x1dGU7CiAgICB0b3A6IDA7CiAgICBsZWZ0OiAwOwogICAgb3ZlcmZsb3c6IGhpZGRlbjsKICAgIGJvcmRlci1yYWRpdXM6IDVweDsKICAgIGJhY2tncm91bmQ6ICMwNDA4MTA7CiAgICBib3gtc2hhZG93OgogICAgICAwIDAgMCAxcHggcmdiYSgxNDgsIDE2MywgMTg0LCAwLjE4KSwKICAgICAgMCAyOHB4IDcwcHggLTE4cHggcmdiYSgwLCAwLCAwLCAwLjgpLAogICAgICAwIDEwcHggMjRweCAtMTJweCByZ2JhKDIsIDYsIDIzLCAwLjgpOwogICAgd2lsbC1jaGFuZ2U6IHRyYW5zZm9ybTsKICB9CgogIC5wb3N0ZXItaWZyYW1lIHsKICAgIGRpc3BsYXk6IGJsb2NrOwogICAgd2lkdGg6IDEwMCU7CiAgICBoZWlnaHQ6IDEwMCU7CiAgICBib3JkZXI6IDA7CiAgICBiYWNrZ3JvdW5kOiAjMDQwODEwOwogIH0KCiAgLnBvc3Rlci1zdXJmYWNlLXNoaWVsZCB7CiAgICBwb3NpdGlvbjogYWJzb2x1dGU7CiAgICBpbnNldDogMDsKICAgIHotaW5kZXg6IDEwOwogICAgdG91Y2gtYWN0aW9uOiBub25lOwogICAgY3Vyc29yOiBkZWZhdWx0OwogIH0KCiAgLnBvc3Rlci1zdGFnZS5pcy16b29tZWQgLnBvc3Rlci1zdXJmYWNlLXNoaWVsZCB7CiAgICBjdXJzb3I6IGdyYWI7CiAgfQoKICAucG9zdGVyLXN0YWdlLmlzLXBhbm5pbmcgLnBvc3Rlci1zdXJmYWNlLXNoaWVsZCB7CiAgICBjdXJzb3I6IGdyYWJiaW5nOwogIH0KCiAgLnBvc3Rlci1zdGFnZS1oaW50IHsKICAgIHBvc2l0aW9uOiBhYnNvbHV0ZTsKICAgIHJpZ2h0OiAxNnB4OwogICAgYm90dG9tOiAxNHB4OwogICAgei1pbmRleDogMjA7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogNXB4OwogICAgcGFkZGluZzogNXB4IDEwcHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDE0OCwgMTYzLCAxODQsIDAuMTgpOwogICAgYm9yZGVyLXJhZGl1czogOTk5cHg7CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDIsIDYsIDIzLCAwLjcyKTsKICAgIGNvbG9yOiByZ2JhKDIwMywgMjEzLCAyMjUsIDAuNzUpOwogICAgZm9udC1zaXplOiAxMHB4OwogICAgbGluZS1oZWlnaHQ6IDEuNDsKICAgIHBvaW50ZXItZXZlbnRzOiBub25lOwogICAgLXdlYmtpdC1iYWNrZHJvcC1maWx0ZXI6IGJsdXIoOHB4KTsKICAgIGJhY2tkcm9wLWZpbHRlcjogYmx1cig4cHgpOwogICAgb3BhY2l0eTogMC45OwogIH0KCiAgLnBvc3Rlci1zdGFnZS1oaW50IC5wb3N0ZXItaGludC1kb3QgewogICAgY29sb3I6IHJnYmEoMTQ4LCAxNjMsIDE4NCwgMC41KTsKICB9CgogIC5wb3N0ZXItaGludC10b3VjaCB7CiAgICBkaXNwbGF5OiBub25lOwogIH0KCiAgQG1lZGlhIChob3Zlcjogbm9uZSksCiAgKHBvaW50ZXI6IGNvYXJzZSksCiAgKG1heC13aWR0aDogNzIwcHgpIHsKICAgIC5wb3N0ZXItaGludC1wb2ludGVyIHsKICAgICAgZGlzcGxheTogbm9uZTsKICAgIH0KCiAgICAucG9zdGVyLWhpbnQtdG91Y2ggewogICAgICBkaXNwbGF5OiBpbmxpbmU7CiAgICB9CiAgfQoKICAvKiAtLS0tLS0tLS0tIOWKoOi9vemBrue9qSAtLS0tLS0tLS0tICovCiAgLnBvc3Rlci1sb2FkaW5nIHsKICAgIHBvc2l0aW9uOiBhYnNvbHV0ZTsKICAgIGluc2V0OiAwOwogICAgei1pbmRleDogMzA7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGp1c3RpZnktY29udGVudDogY2VudGVyOwogICAgcGFkZGluZzogMjRweDsKICAgIGJveC1zaXppbmc6IGJvcmRlci1ib3g7CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDgsIDEzLCAyMywgMC45Mik7CiAgICAtd2Via2l0LWJhY2tkcm9wLWZpbHRlcjogYmx1cigzcHgpOwogICAgYmFja2Ryb3AtZmlsdGVyOiBibHVyKDNweCk7CiAgfQoKICAucG9zdGVyLWxvYWRpbmctY2FyZCB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogMTNweDsKICAgIG1heC13aWR0aDogMzQwcHg7CiAgICBwYWRkaW5nOiAxNXB4IDE3cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDE0OCwgMTYzLCAxODQsIDAuMjIpOwogICAgYm9yZGVyLXJhZGl1czogMTNweDsKICAgIGJhY2tncm91bmQ6IHJnYmEoMzAsIDQxLCA1OSwgMC45NSk7CiAgICBib3gtc2hhZG93OiAwIDI0cHggNjBweCAtMjBweCByZ2JhKDAsIDAsIDAsIDAuNyk7CiAgfQoKICAucG9zdGVyLWxvYWRpbmctY29weSB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgZmxleC1kaXJlY3Rpb246IGNvbHVtbjsKICAgIGdhcDogM3B4OwogICAgbWluLXdpZHRoOiAwOwogIH0KCiAgLnBvc3Rlci1sb2FkaW5nLWNvcHkgc3Ryb25nIHsKICAgIGNvbG9yOiAjZjhmYWZjOwogICAgZm9udC1zaXplOiAxMnB4OwogICAgZm9udC13ZWlnaHQ6IDYwMDsKICAgIGxpbmUtaGVpZ2h0OiAxLjU7CiAgfQoKICAucG9zdGVyLWxvYWRpbmctY29weSBzcGFuIHsKICAgIGNvbG9yOiAjOTRhM2I4OwogICAgZm9udC1zaXplOiAxMHB4OwogICAgbGluZS1oZWlnaHQ6IDEuNjU7CiAgICBvdmVyZmxvdy13cmFwOiBhbnl3aGVyZTsKICB9CgogIC5wb3N0ZXItc3Bpbm5lciB7CiAgICBkaXNwbGF5OiBpbmxpbmUtYmxvY2s7CiAgICBmbGV4LXNocmluazogMDsKICAgIHdpZHRoOiAxMnB4OwogICAgaGVpZ2h0OiAxMnB4OwogICAgYm94LXNpemluZzogYm9yZGVyLWJveDsKICAgIGJvcmRlcjogMnB4IHNvbGlkIHJnYmEoMTQ4LCAxNjMsIDE4NCwgMC4yOCk7CiAgICBib3JkZXItdG9wLWNvbG9yOiBjdXJyZW50Q29sb3I7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGFuaW1hdGlvbjogcG9zdGVyLXNwaW4gMC44cyBsaW5lYXIgaW5maW5pdGU7CiAgfQoKICAucG9zdGVyLXNwaW5uZXItLWxhcmdlIHsKICAgIHdpZHRoOiAyNnB4OwogICAgaGVpZ2h0OiAyNnB4OwogICAgYm9yZGVyLXdpZHRoOiAzcHg7CiAgICBib3JkZXItdG9wLWNvbG9yOiAjMjJkM2VlOwogIH0KCiAgLnBvc3Rlci1mb3JtYXQtYnRuIC5wb3N0ZXItc3Bpbm5lciB7CiAgICB3aWR0aDogMTFweDsKICAgIGhlaWdodDogMTFweDsKICAgIGJvcmRlci13aWR0aDogMnB4OwogICAgYm9yZGVyLXRvcC1jb2xvcjogY3VycmVudENvbG9yOwogIH0KCiAgQGtleWZyYW1lcyBwb3N0ZXItc3BpbiB7CiAgICB0byB7CiAgICAgIHRyYW5zZm9ybTogcm90YXRlKDM2MGRlZyk7CiAgICB9CiAgfQoKICAvKiAtLS0tLS0tLS0tIOW6lemDqOS/oeaBryAtLS0tLS0tLS0tICovCiAgLnBvc3Rlci1mb290ZXIgewogICAgZGlzcGxheTogZmxleDsKICAgIGZsZXgtd3JhcDogd3JhcDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IHNwYWNlLWJldHdlZW47CiAgICBnYXA6IDZweCAxNnB4OwogICAgZmxleC1zaHJpbms6IDA7CiAgICBtYXJnaW4tdG9wOiAxMHB4OwogICAgcGFkZGluZy10b3A6IDEwcHg7CiAgICBib3JkZXItdG9wOiAxcHggc29saWQgI2VlZjJmNzsKICAgIGNvbG9yOiAjOTRhM2I4OwogICAgZm9udC1zaXplOiAxMHB4OwogICAgbGluZS1oZWlnaHQ6IDEuNjsKICB9CgogIC5wb3N0ZXItZm9vdGVyLW1ldGEgewogICAgZGlzcGxheTogaW5saW5lLWZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiA2cHg7CiAgICBtaW4td2lkdGg6IDA7CiAgfQoKICAucG9zdGVyLWZvb3Rlci1iYWRnZSB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBoZWlnaHQ6IDE3cHg7CiAgICBwYWRkaW5nOiAwIDVweDsKICAgIGJvcmRlci1yYWRpdXM6IDRweDsKICAgIGJhY2tncm91bmQ6ICNlZWYyZjc7CiAgICBjb2xvcjogIzY0NzQ4YjsKICAgIGZvbnQtZmFtaWx5OiB1aS1tb25vc3BhY2UsIFNGTW9uby1SZWd1bGFyLCBNZW5sbywgTW9uYWNvLCBDb25zb2xhcywgbW9ub3NwYWNlOwogICAgZm9udC1zaXplOiA5cHg7CiAgICBmb250LXdlaWdodDogNjAwOwogICAgbGluZS1oZWlnaHQ6IDE7CiAgfQoKICAucG9zdGVyLWZvb3Rlci1leHBvcnQgewogICAgY29sb3I6ICMwNTk2Njk7CiAgICBmb250LXdlaWdodDogNTAwOwogICAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7CiAgfQoKICAucG9zdGVyLWZvb3Rlci10aXAgewogICAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7CiAgfQoKICAvKiAtLS0tLS0tLS0tIOmihOiniOW8guW4uOW8ueeqlyAtLS0tLS0tLS0tICovCiAgLnBvc3Rlci1lcnJvci1tYXNrIHsKICAgIHBvc2l0aW9uOiBmaXhlZDsKICAgIGluc2V0OiAwOwogICAgei1pbmRleDogMjE0NzQ4MzAwMDsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBwYWRkaW5nOiAyMHB4OwogICAgYmFja2dyb3VuZDogcmdiYSgxNSwgMjMsIDQyLCAwLjU4KTsKICAgIC13ZWJraXQtYmFja2Ryb3AtZmlsdGVyOiBibHVyKDNweCk7CiAgICBiYWNrZHJvcC1maWx0ZXI6IGJsdXIoM3B4KTsKICB9CgogIC5wb3N0ZXItZXJyb3ItZGlhbG9nIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBmbGV4LWRpcmVjdGlvbjogY29sdW1uOwogICAgd2lkdGg6IDEwMCU7CiAgICBtYXgtd2lkdGg6IDU2MHB4OwogICAgbWF4LWhlaWdodDogODJ2aDsKICAgIG92ZXJmbG93OiBoaWRkZW47CiAgICBib3JkZXI6IDFweCBzb2xpZCAjZTJlOGYwOwogICAgYm9yZGVyLXJhZGl1czogMTVweDsKICAgIGJhY2tncm91bmQ6ICNmZmZmZmY7CiAgICBib3gtc2hhZG93OiAwIDMwcHggODBweCAtMjBweCByZ2JhKDE1LCAyMywgNDIsIDAuNDUpOwogICAgb3V0bGluZTogbm9uZTsKICB9CgogIC5wb3N0ZXItZXJyb3ItaGVhZGVyIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogZmxleC1zdGFydDsKICAgIGp1c3RpZnktY29udGVudDogc3BhY2UtYmV0d2VlbjsKICAgIGdhcDogMTJweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgcGFkZGluZzogMTZweCAyMHB4IDE0cHg7CiAgICBib3JkZXItYm90dG9tOiAxcHggc29saWQgI2VlZjJmNzsKICB9CgogIC5wb3N0ZXItZXJyb3ItaGVhZGluZyB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGZsZXgtc3RhcnQ7CiAgICBnYXA6IDExcHg7CiAgICBtaW4td2lkdGg6IDA7CiAgfQoKICAucG9zdGVyLWVycm9yLWJhZGdlIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBmbGV4LXNocmluazogMDsKICAgIHdpZHRoOiAyOHB4OwogICAgaGVpZ2h0OiAyOHB4OwogICAgYm9yZGVyLXJhZGl1czogOTk5cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmVmMmYyOwogICAgY29sb3I6ICNkYzI2MjY7CiAgICBmb250LXNpemU6IDE1cHg7CiAgICBmb250LXdlaWdodDogNzAwOwogICAgbGluZS1oZWlnaHQ6IDE7CiAgfQoKICAucG9zdGVyLWVycm9yLXRpdGxlIHsKICAgIG1hcmdpbjogMDsKICAgIGNvbG9yOiAjMGYxNzJhOwogICAgZm9udC1zaXplOiAxNHB4OwogICAgZm9udC13ZWlnaHQ6IDY1MDsKICAgIGxpbmUtaGVpZ2h0OiAxLjU7CiAgfQoKICAucG9zdGVyLWVycm9yLXN1YnRpdGxlIHsKICAgIG1hcmdpbjogMnB4IDAgMDsKICAgIGNvbG9yOiAjNjQ3NDhiOwogICAgZm9udC1zaXplOiAxMXB4OwogICAgbGluZS1oZWlnaHQ6IDEuNTsKICB9CgogIC5wb3N0ZXItZXJyb3ItY2xvc2UgewogICAgZmxleC1zaHJpbms6IDA7CiAgICB3aWR0aDogMjhweDsKICAgIGhlaWdodDogMjhweDsKICAgIHBhZGRpbmc6IDA7CiAgICBib3JkZXI6IDA7CiAgICBib3JkZXItcmFkaXVzOiA4cHg7CiAgICBiYWNrZ3JvdW5kOiB0cmFuc3BhcmVudDsKICAgIGNvbG9yOiAjOTRhM2I4OwogICAgZm9udC1zaXplOiAyMnB4OwogICAgbGluZS1oZWlnaHQ6IDE7CiAgICBjdXJzb3I6IHBvaW50ZXI7CiAgICB0cmFuc2l0aW9uOiBiYWNrZ3JvdW5kLWNvbG9yIDAuMTVzIGVhc2UsIGNvbG9yIDAuMTVzIGVhc2U7CiAgfQoKICAucG9zdGVyLWVycm9yLWNsb3NlOmhvdmVyIHsKICAgIGJhY2tncm91bmQ6ICNmMWY1Zjk7CiAgICBjb2xvcjogIzQ3NTU2OTsKICB9CgogIC5wb3N0ZXItZXJyb3ItY2xvc2U6Zm9jdXMtdmlzaWJsZSwKICAucG9zdGVyLWVycm9yLWJ0bjpmb2N1cy12aXNpYmxlIHsKICAgIG91dGxpbmU6IDJweCBzb2xpZCAjNjM2NmYxOwogICAgb3V0bGluZS1vZmZzZXQ6IDJweDsKICB9CgogIC5wb3N0ZXItZXJyb3ItYm9keSB7CiAgICBmbGV4OiAxIDEgYXV0bzsKICAgIHBhZGRpbmc6IDEycHggMjBweDsKICAgIG92ZXJmbG93OiBhdXRvOwogICAgYmFja2dyb3VuZDogI2Y4ZmFmYzsKICB9CgogIC5wb3N0ZXItZXJyb3ItaXRlbSB7CiAgICBtYXJnaW4tYm90dG9tOiAxMHB4OwogICAgcGFkZGluZzogMTFweCAxM3B4OwogICAgYm9yZGVyOiAxcHggc29saWQgI2UyZThmMDsKICAgIGJvcmRlci1yYWRpdXM6IDEwcHg7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogIH0KCiAgLnBvc3Rlci1lcnJvci1pdGVtOmxhc3QtY2hpbGQgewogICAgbWFyZ2luLWJvdHRvbTogMDsKICB9CgogIC5wb3N0ZXItZXJyb3ItaXRlbS1oZWFkIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBzcGFjZS1iZXR3ZWVuOwogICAgZ2FwOiAxMHB4OwogICAgbWFyZ2luLWJvdHRvbTogNXB4OwogIH0KCiAgLnBvc3Rlci1lcnJvci10eXBlIHsKICAgIHBhZGRpbmc6IDFweCA3cHg7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGJhY2tncm91bmQ6ICNmZWYyZjI7CiAgICBjb2xvcjogI2I5MWMxYzsKICAgIGZvbnQtc2l6ZTogMTBweDsKICAgIGZvbnQtd2VpZ2h0OiA2MDA7CiAgICBsaW5lLWhlaWdodDogMS42OwogIH0KCiAgLnBvc3Rlci1lcnJvci10aW1lIHsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgY29sb3I6ICM5NGEzYjg7CiAgICBmb250LXNpemU6IDEwcHg7CiAgfQoKICAucG9zdGVyLWVycm9yLW1lc3NhZ2UgewogICAgbWFyZ2luOiAwIDAgNXB4OwogICAgY29sb3I6ICMwZjE3MmE7CiAgICBmb250LXNpemU6IDEycHg7CiAgICBsaW5lLWhlaWdodDogMS42OwogICAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7CiAgICB3aGl0ZS1zcGFjZTogcHJlLXdyYXA7CiAgfQoKICAucG9zdGVyLWVycm9yLWxvY2F0aW9uIHsKICAgIG1hcmdpbjogMCAwIDVweDsKICAgIGNvbG9yOiAjNjQ3NDhiOwogICAgZm9udC1zaXplOiAxMXB4OwogICAgbGluZS1oZWlnaHQ6IDEuNTsKICAgIG92ZXJmbG93LXdyYXA6IGFueXdoZXJlOwogIH0KCiAgLnBvc3Rlci1lcnJvci1zdGFjayB7CiAgICBtYXgtaGVpZ2h0OiAyMDBweDsKICAgIG1hcmdpbjogMDsKICAgIHBhZGRpbmc6IDlweCAxMXB4OwogICAgb3ZlcmZsb3c6IGF1dG87CiAgICBib3JkZXItcmFkaXVzOiA4cHg7CiAgICBiYWNrZ3JvdW5kOiAjMGYxNzJhOwogICAgY29sb3I6ICNjYmQ1ZTE7CiAgICBmb250LWZhbWlseTogdWktbW9ub3NwYWNlLCBTRk1vbm8tUmVndWxhciwgTWVubG8sIE1vbmFjbywgQ29uc29sYXMsICJMaWJlcmF0aW9uIE1vbm8iLCBtb25vc3BhY2U7CiAgICBmb250LXNpemU6IDEwcHg7CiAgICBsaW5lLWhlaWdodDogMS42OwogICAgd2hpdGUtc3BhY2U6IHByZS13cmFwOwogICAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7CiAgfQoKICAucG9zdGVyLWVycm9yLWZvb3RlciB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgZmxleC13cmFwOiB3cmFwOwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGp1c3RpZnktY29udGVudDogZmxleC1lbmQ7CiAgICBnYXA6IDhweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgcGFkZGluZzogMTJweCAyMHB4OwogICAgYm9yZGVyLXRvcDogMXB4IHNvbGlkICNlMmU4ZjA7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogIH0KCiAgLnBvc3Rlci1lcnJvci1jb3B5LWhpbnQgewogICAgbWFyZ2luLXJpZ2h0OiBhdXRvOwogICAgY29sb3I6ICMxNmEzNGE7CiAgICBmb250LXNpemU6IDExcHg7CiAgfQoKICAucG9zdGVyLWVycm9yLWNvcHktaGludC5pcy1lcnJvciB7CiAgICBjb2xvcjogI2RjMjYyNjsKICB9CgogIC5wb3N0ZXItZXJyb3ItYnRuIHsKICAgIGhlaWdodDogMzBweDsKICAgIHBhZGRpbmc6IDAgMTNweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkICNjYmQ1ZTE7CiAgICBib3JkZXItcmFkaXVzOiA4cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogICAgY29sb3I6ICMzMzQxNTU7CiAgICBmb250LXNpemU6IDExcHg7CiAgICBmb250LXdlaWdodDogNTAwOwogICAgbGluZS1oZWlnaHQ6IDE7CiAgICBjdXJzb3I6IHBvaW50ZXI7CiAgICB0cmFuc2l0aW9uOiBiYWNrZ3JvdW5kLWNvbG9yIDAuMTVzIGVhc2UsIGJvcmRlci1jb2xvciAwLjE1cyBlYXNlLCBjb2xvciAwLjE1cyBlYXNlOwogIH0KCiAgLnBvc3Rlci1lcnJvci1idG46aG92ZXI6bm90KDpkaXNhYmxlZCkgewogICAgYm9yZGVyLWNvbG9yOiAjYTViNGZjOwogICAgYmFja2dyb3VuZDogI2VlZjJmZjsKICAgIGNvbG9yOiAjNDMzOGNhOwogIH0KCiAgLnBvc3Rlci1lcnJvci1idG46ZGlzYWJsZWQgewogICAgb3BhY2l0eTogMC42OwogICAgY3Vyc29yOiBub3QtYWxsb3dlZDsKICB9CgogIC5wb3N0ZXItZXJyb3ItYnRuLS1wcmltYXJ5IHsKICAgIGJvcmRlci1jb2xvcjogIzQzMzhjYTsKICAgIGJhY2tncm91bmQ6ICM0MzM4Y2E7CiAgICBjb2xvcjogI2ZmZmZmZjsKICB9CgogIC5wb3N0ZXItZXJyb3ItYnRuLS1wcmltYXJ5OmhvdmVyOm5vdCg6ZGlzYWJsZWQpIHsKICAgIGJvcmRlci1jb2xvcjogIzM3MzBhMzsKICAgIGJhY2tncm91bmQ6ICMzNzMwYTM7CiAgICBjb2xvcjogI2ZmZmZmZjsKICB9CgogIC8qIC0tLS0tLS0tLS0g6L+H5rihIC0tLS0tLS0tLS0gKi8KICAucG9zdGVyLWZhZGUtZW50ZXItYWN0aXZlLAogIC5wb3N0ZXItZmFkZS1sZWF2ZS1hY3RpdmUgewogICAgdHJhbnNpdGlvbjogb3BhY2l0eSAwLjE4cyBlYXNlOwogIH0KCiAgLnBvc3Rlci1mYWRlLWVudGVyLAogIC5wb3N0ZXItZmFkZS1lbnRlci1mcm9tLAogIC5wb3N0ZXItZmFkZS1sZWF2ZS10byB7CiAgICBvcGFjaXR5OiAwOwogIH0KCiAgLyogLS0tLS0tLS0tLSDlk43lupTlvI8gLS0tLS0tLS0tLSAqLwogIEBtZWRpYSAobWF4LXdpZHRoOiA3MjBweCkgewogICAgLnBvc3Rlci12aWV3ZXIgewogICAgICBwYWRkaW5nOiAxMnB4OwogICAgICBib3JkZXItcmFkaXVzOiAxM3B4OwogICAgfQoKICAgIC5wb3N0ZXItdGl0bGUgewogICAgICBmb250LXNpemU6IDE2cHg7CiAgICB9CgogICAgLnBvc3Rlci1kZXNjIHsKICAgICAgZm9udC1zaXplOiAxMXB4OwogICAgfQoKICAgIC5wb3N0ZXItdG9vbGJhciB7CiAgICAgIG1hcmdpbjogOHB4IDA7CiAgICAgIHBhZGRpbmc6IDdweCA4cHg7CiAgICB9CgogICAgLnBvc3Rlci1zdGFnZSB7CiAgICAgIHBhZGRpbmc6IDE2cHg7CiAgICB9CgogICAgLnBvc3Rlci1zdGFnZS1oaW50IHsKICAgICAgcmlnaHQ6IDEwcHg7CiAgICAgIGJvdHRvbTogOXB4OwogICAgICBmb250LXNpemU6IDlweDsKICAgIH0KCiAgICAucG9zdGVyLWZvb3Rlci10aXAgewogICAgICBkaXNwbGF5OiBub25lOwogICAgfQoKICAgIC5wb3N0ZXItbG9hZGluZyB7CiAgICAgIHBhZGRpbmc6IDE2cHg7CiAgICB9CgogICAgLnBvc3Rlci1sb2FkaW5nLWNhcmQgewogICAgICBtYXgtd2lkdGg6IDEwMCU7CiAgICAgIHBhZGRpbmc6IDEzcHggMTVweDsKICAgIH0KCiAgICAucG9zdGVyLWVycm9yLW1hc2sgewogICAgICBwYWRkaW5nOiAxMnB4OwogICAgfQoKICAgIC5wb3N0ZXItZXJyb3ItZGlhbG9nIHsKICAgICAgbWF4LWhlaWdodDogOTB2aDsKICAgIH0KCiAgICAucG9zdGVyLWVycm9yLWhlYWRlciwKICAgIC5wb3N0ZXItZXJyb3ItYm9keSwKICAgIC5wb3N0ZXItZXJyb3ItZm9vdGVyIHsKICAgICAgcGFkZGluZy1sZWZ0OiAxNHB4OwogICAgICBwYWRkaW5nLXJpZ2h0OiAxNHB4OwogICAgfQogIH0KCiAgQG1lZGlhIChtYXgtd2lkdGg6IDUyMHB4KSB7CiAgICAucG9zdGVyLWV4cG9ydC1sYWJlbCB7CiAgICAgIGRpc3BsYXk6IG5vbmU7CiAgICB9CgogICAgLnBvc3Rlci1mb3JtYXQtYnRuIHsKICAgICAgbWluLXdpZHRoOiA0OHB4OwogICAgICBwYWRkaW5nOiAwIDlweDsKICAgIH0KCiAgICAucG9zdGVyLWRvd25sb2FkLWNoaXAgewogICAgICBwYWRkaW5nOiAwIDhweDsKICAgIH0KCiAgICAucG9zdGVyLXN0YXR1c2JhciB7CiAgICAgIHdpZHRoOiAxMDAlOwogICAgICBqdXN0aWZ5LWNvbnRlbnQ6IGZsZXgtc3RhcnQ7CiAgICB9CgogICAgLnBvc3Rlci1mb290ZXIgewogICAgICBmb250LXNpemU6IDlweDsKICAgIH0KICB9Cjwvc3R5bGU+"
									}
								},
      exampleList: [{
            "name": "先锋音乐节",
            "description": "瑞士国际主义网格排版海报：非对称粗衬线大字、高对比酸性霓虹配色、噪点质感叠加与可编辑演出阵容模块。",
            "icon": "🎛️"
          },
          {
            "name": "建筑双年展",
            "description": "极简包豪斯几何构图海报：黄金分割比例留白、等轴测线框图形、精细字距排印与一键切换多语言展览导视。",
            "icon": "🏛️"
          },
          {
            "name": "精品咖啡特调",
            "description": "复古波普版画风商业海报：半调网点阴影、手绘感矢量杯具插画、暖调复古纸张纹理与实时替换风味标签面板。",
            "icon": "☕"
          },
          {
            "name": "AI 开发者大会",
            "description": "现代科技感主视觉海报：程序化流体渐变光效、毛玻璃信息卡片、等宽代码装饰元素与高分辨率 PNG/SVG 导出。",
            "icon": "🚀"
          }
        ],
      loadingProgressApp: {
          className: "ElementVueItem",
          task: {
            template: "/webApps/document/aigcLoading.vue",
          },
        },
      feedbackDialog: {
          visible: false,
          placeholder: "输入问题描述或建议内容。",
          message: "",
        },
      interface: {
          sendFeedback: {
            url: webCpu.documentPrefix + "/user/sendFeedback",
            method: "post",
          },
        },
      styleIndex: "",
      content: {
          inputText: "",
          outputText: "",
        },
      inputPlaceholder: "输入海报的内容和要求...",
      description: "",
      query: {"documentId":"c19fca2dd60e295a12948cd241fb5b23"},
      accountData: {},
      flag: WebTool.adjustSize([false, false, true]),
      isSolo: WebTool.urlQuery(location.href, "solo"),
      defaultTemplate: "",
      dataEditable: true,
      title: "LeaferJS 海报",
      introduce: "生成基于 leafer.js 的海报。",
      icon: "🧊"
    };
},"computed":{"myAccept":function() {
        return `.html, .md, .txt, .doc, .docx, .pdf, .ppt, .pptx, .xlsx, .xls, .csv`;
      },"viewMode":function() {
        return !this.modifyEnabled && !!this.currentVersionIndex;
      },"resultPanelHeight":function() {
        let ret = "";
        if (this.isSolo || this.content.isSolo) {
          if (this.step === 1) {
            ret = "100%";
          } else {
            ret = "calc( 100% - 165px )";
          }
        } else {
          if (this.step === 1) {
            ret = "calc( 100% - 50px )";
          } else {
            ret = "calc( 100% - 170px )";
          }
        }

        return ret;
      },"documentId":function() {
        let documentId = this.query.documentId;
        if (
          !documentId &&
          webCpu.currentCard.task &&
          webCpu.currentCard.task.vueItem &&
          webCpu.currentCard.task.vueItem.documentMeta
        ) {
          documentId = webCpu.currentCard.task.vueItem.documentMeta.documentId;
        }
        return documentId;
      },"currentTips":function() {
        let ret = this.waitingText;
        if (this.loading) {
          ret = this.loadingText;
        } else if (this.description || this.content.inputText) {
          ret = this.gettedTips;
        } else {}

        return ret;
      }},"methods":{"startModify":function() {
        this.$confirm(
            "当前版本为历史版本，是否基于当前版本进行修改？",
            webCpu.messages[lang].confirmDialogTitle, {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          )
          .then(() => {
            this.modifyEnabled = true;
            // let tJson = WebTool.extractJSON(
            //   this.versionList[this.currentVersionIndex].result
            // );
            // this.updateResult(tJson);
          })
          .catch(() => {
            // 取消操作
          });
      },"checkDiff":function(index) {
        let e = window.event;
        e.stopPropagation();
        e.preventDefault();
        let style = {
          width: "100%",
          height: "100%",
        };
        let versionA = this.versionList[this.currentVersionIndex].result;
        let versionB = this.versionList[index].result;
        let baseContent = JSON.stringify(this.accountData);
        let elem = this.$refs.loadingEffectArea;
        let confirmCallback = () => {
          let content = diffCheckApp.card.task.vueItem.generateNewVersion();
          let currentId = MurmurHash.rule(baseContent, 31);
          let tId = MurmurHash.rule(JSON.stringify(content), 31);
          if (tId !== currentId) {
            this.confirmContent(content);
          } else {
            this.$message({
              type: "info",
              message: "内容没有变更。",
            });
          }
          webCpu.CardItem.dismissMask(elem);
        };
        let closeCallback = () => {
          let content = diffCheckApp.card.task.vueItem.generateNewVersion();
          let currentId = MurmurHash.rule(baseContent, 31);
          let tId = MurmurHash.rule(JSON.stringify(content), 31);
          if (tId !== currentId) {
            this.$confirm(
              "检测到有内容变更，是否生成新版本？",
              webCpu.messages[lang].confirmDialogTitle, {
                confirmButtonText: webCpu.messages[lang].confirmYes,
                cancelButtonText: webCpu.messages[lang].confirmNo,
                type: "warning",
              }
            ).then(() => {
              this.confirmContent(content);
            });
          }
          webCpu.CardItem.dismissMask(elem);
        };
        let diffCheckApp = {
          className: "ElementVueItem",
          task: {
            template: webCpu.documentPrefix + "/transweb/diffCheckApp.vue",
            data: {
              versionA: WebTool.extractJSON(versionA),
              versionB: WebTool.extractJSON(versionB),
            },
            style: {
              width: "100%",
              height: "100%",
              overflow: "auto",
            },
          },
        };

        webCpu.renderCardDialog(
          elem,
          diffCheckApp, {
            dialogName: "checkDiffDialog",
            title: "版本差异检查",
            closeType: "back",
            titleStyle: {
              background: "var(--el-color-primary-light-9)",
              color: "var(--el-color-primary)",
            },
            menu: [{
              label: "确认",
              action: confirmCallback,
            }, ],
            closeCallback: closeCallback,
          },
          style, {
            padding: "20px 30px 20px 30px",
          }
        );
      },"updateVersionList":function(versionId, callback) {
        versionId = versionId || JSON.stringify(this.accountData);
        aigcInterface.getHistoryVersion(versionId, (tData) => {
          if (tData && tData.ret === 0) {
            tData.data = tData.data || [];
            if (tData.data.length > 0) {
              this.versionList = tData.data.sort((a, b) => {
                return b._time - a._time;
              });
              for (let i = 0; i < this.versionList.length; i++) {
                let item = this.versionList[i];
                let jsonStr = JSON.stringify(WebTool.extractJSON(item.result));
                let temp = JSON.stringify(this.accountData);
                if (this.currentVersion === item.versionId || jsonStr === temp) {
                  this.currentVersionIndex = i;
                  this.currentVersion = "";
                  break;
                }
              }
              if (typeof callback === "function") {
                callback();
              }
            } else {
              this.accountData = this.accountData || {};
              aigcInterface.recordDataVersion(this.accountData, "", (tData) => {
                console.log(tData);
                let content = JSON.stringify(this.accountData);
                this.updateVersionList(content);
                this.currentVersionIndex = 0;
                if (typeof callback === "function") {
                  callback();
                }
              });
            }
          }
        });
      },"getVersionLabel":function(item) {
        if (!item || !item.versionId) {
          return false;
        }
        let str = item.versionId.slice(0, 5);
        str = `${str}@${new Date(item._time).toLocaleString()}`;
        return str;
      },"handleVersionChange":function(index) {
        this.versionList = this.versionList || [];
        console.log(index, this.versionList[index]);
        if (this.versionList[index] && this.versionList[index].result) {
          this.isStreaming = true;
          this.switchResultStatus(this.versionList[index].result, true);
          this.currentVersionIndex = index;
          this.modifyEnabled = false;
        }
      },"handleConfirm":function() {
        // 1. 获取并清理输入，防止纯空格绕过校验
        const requirement = this.modifyInfo?.trim();

        if (!requirement) {
          this.$message({
            type: "warning",
            message: "需要输入变更描述。",
          });
          return false;
        }

        // 2. 结构化 Prompt 模板，加入强制约束
        const promptContext = `
你是一个严谨的数据处理助手。请根据【修改需求】修改【原始数据】，并严格遵守【输出要求】。

【修改需求】
${requirement}

【原始数据】
${JSON.stringify(this.accountData)}

【输出要求】
1. 必须输出修改后的完整内容，保持原有的格式和层级结构完全一致。
2. 绝对不允许删减、折叠或省略【原始数据】中未要求修改的任何字段和细节。
3. 仅输出最终结果，不要包含任何开场白、解释性废话，也不要输出 Markdown 代码块标记（如 \`\`\` 等）。
`.trim();

        // 3. 执行生成
        this.modifyGenerate({
          documentId: this.query.documentId,
          context: promptContext,
        });
      },"triggerFileInput":function(e) {
        e = e || window.event;
        e.stopPropagation();
        e.preventDefault();
        this.$refs.hiddenFileInput.click();
      },"confirmTransfer":function(text) {
        this.$confirm(
          "文件中内容结构 Agent 所支持不一致，是否生成相应结构化内容？",
          webCpu.messages[lang].confirmDialogTitle, {
            confirmButtonText: webCpu.messages[lang].confirmYes,
            cancelButtonText: webCpu.messages[lang].confirmNo,
            type: "warning",
          }
        ).then(() => {
          this.content.inputText = text;
          this.handleGenerate();
        });
      },"checkDataStruct":function(json, template) {
        template = WebTool.base64ToUtf8(template);
        let obj = webCpu.getDslObj(template) || {};
        let data = typeof obj.data === "function" ? obj.data().accountData : {};
        let ret =
          Object.keys(json || {}).sort().join(",") ===
          Object.keys(data || {}).sort().join(",");
        return ret;
      },"onFileSelected":async function(event) {
        const file = event.target.files[0];
        if (!file) return;
        this.readingFile = true;
        let arr = file.name.split(".");
        let subfix = arr[arr.length - 1];

        if (subfix !== "html" && subfix !== "txt" && subfix !== "md") {
          let temp = await WebTool.extractTextFromFile(file);
          this.confirmTransfer(temp);
          this.readingFile = false;
          event.target.value = "";
          return true;
        }

        const reader = new FileReader();
        // 读取为文本
        reader.onload = (e) => {
          let text = e.target.result;

          if (subfix === "html") {
            let d = WebTool.extractAppTaskData(text);
            if (d && d.data && d.data.accountData) {
              this.isStreaming = true;
              let tData = d.data.accountData || d.data;
              this.switchResultStatus(tData);
            } else {
              this.confirmTransfer(text);
            }
          } else {
            let json = WebTool.markdownToObject(text);
            let ret = this.checkDataStruct(
              json,
              this.currentAppItem.task.template
            );
            if (json && ret) {
              this.isStreaming = true;
              this.switchResultStatus(json);
            } else {
              this.confirmTransfer(text);
            }
          }

          this.readingFile = false;
        };
        // ⚠️ 关键：按文本读取
        reader.readAsText(file, "utf-8");
        event.target.value = "";
      },"openFeedbackDialog":function() {
        this.feedbackDialog.visible = true;
      },"confirmSendFeedback":function() {
        let tempData = JSON.parse(JSON.stringify(this.accountData));
        if (!this.feedbackDialog.message.trim()) {
          this.$message({
            type: "warning",
            message: "需要输入有效问题描述。",
          });
          return false;
        }
        tempData.feedback = this.feedbackDialog.message;
        tempData.documentId = this.query.documentId;
        this.feedbackDialog.visible = false;
        this.adapter.sendFeedback(tempData, (tData) => {
          this.$message({
            type: "success",
            message: "感谢反馈！",
          });
        });
      },"handleContentCommand":function(type) {
        let params = this.accountData;
        if (type === "htmlDownload") {
          this.downloadHtmlItem({
            content: JSON.stringify(params),
            name: params.name || params.title,
            template: this.currentAppItem.task.template,
          });
        } else if (type === "markdownDownload") {
          this.downloadMarkdown(params);
        } else if (type === "readLocal") {
          this.triggerFileInput();
        } else if (type == "imageDownload") {
          this.downloadImageItem();
        } else if (type === "copyLink") {
          this.copyLink();
        } else {
          this.openContentEditor();
        }
      },"copyLink":function() {
        let documentId = this.documentId;
        let dataId = this.versionList[this.currentVersionIndex]._id;
        let url = location.origin + "/skill.html?solo=true&dataView=true&dataId=" + dataId + "&id=" + documentId;
        WebTool.copyString(url);
        this.$message({
          type: "success",
          message: "链接已复制到剪贴板。",
        });
      },"downloadImageItem":function() {
        let elem = $(this.$refs.resultComponentItem).find(
          ".ElementVueItem._task_container>div>div"
        )[0];
        if (!elem) {
          this.$message({
            type: "warning",
            message: "当前结果不支持导出图片。",
          });
          return false;
        } else {
          let name = this.accountData.name || this.accountData.title || "result";
          WebTool.transformImage(elem, name, "png", {
            download: true,
          });
        }
      },"searchContent":function(str) {
        let index = -1;
        if (typeof str === "object") {
          str = JSON.stringify(str);
        }
        for (let i = 0; i < this.versionList.length; i++) {
          let item = this.versionList[i];
          let result = JSON.stringify(WebTool.extractJSON(item.result));
          if (result === str) {
            index = i;
            break;
          }
        }
        return index;
      },"confirmContent":function(content) {
        let index = this.searchContent(content);
        let tContent = JSON.stringify(this.accountData);
        this.accountData = content;
        if (index === -1) {
          aigcInterface.recordDataVersion(content, tContent, (tData) => {
            console.log(tData);
            this.$message({
              type: "success",
              message: "已生成新版本。",
            });
            this.updateResult(content);
            this.updateVersionList(content);
            this.currentVersionIndex = 0;
          });
        } else {
          this.$message({
            type: "warning",
            message: "修改后与一个历史版本内容一致。",
          });
          this.$confirm(
            "修改后与一个历史版本内容一致，是否切换到该版本？",
            webCpu.messages[lang].confirmDialogTitle, {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          ).then(() => {
            let tJson = WebTool.extractJSON(this.versionList[index].result);
            this.updateResult(tJson);
            this.accountData = content;
            this.currentVersionIndex = index;
          });
        }
      },"openContentEditor":function() {
        let elem = this.$refs.loadingEffectArea;
        let mdString = WebTool.objectToMarkdown(this.accountData);
        let app = {
          className: "CodeEditor",
          dsl: {
            changeCallback: function(dd) {},
            data: mdString,
            options: {
              lineNumbers: true,
              value: "",
              mode: "markdown",
            },
          },
        };

        let baseContent = JSON.stringify(this.accountData);
        let confirmCallback = () => {
          let content = WebTool.markdownToObject(app.card.task.data);
          let currentId = MurmurHash.rule(baseContent, 31);
          let tId = MurmurHash.rule(content, 31);
          if (tId !== currentId) {
            this.confirmContent(content);
          } else {
            this.$message({
              type: "info",
              message: "内容没有变更。",
            });
          }
          webCpu.CardItem.dismissMask(elem);
        };
        let copyScript = () => {
          let script = app.card.task.data;
          WebTool.copyString(script);
          this.$message({
            type: "success",
            message: "复制成功",
          });
        };
        let menu = [{
            label: "复制",
            action: copyScript,
          },
          {
            label: "确认",
            action: confirmCallback,
          },
        ];
        if (this.viewMode) {
          menu.length = 1;
        }
        webCpu.CardItem.renderCardDialog(
          elem,
          app, {
            title: "编辑内容",
            titleStyle: {
              background: "var(--el-color-primary-light-9)",
              color: "var(--el-color-primary)",
            },
            closeType: "back",
            dialogName: "codeEditorDialog",
            menu: menu,
          }, {
            width: "100%",
            height: "100%",
          }
        );
      },"downloadMarkdown":function(params) {
        let md = WebTool.objectToMarkdown(params);
        let name = params.name || params.title || "未命名内容";
        if (!/\.md$/i.test(name)) {
          name += ".md";
        }
        WebTool.downloadString(md, name);
      },"downloadHtmlItem":function(params) {
        let adapter = webCpu.initAdapter({
          getTemplate: {
            url: webCpu.documentPrefix + "/transweb/components/ElementVueItem/example.html",
            method: "get",
            dataType: "text",
          },
        });
        adapter.getTemplate({}, (html) => {
          let documentId =
            this.query.documentId ||
            webCpu.currentCard.task.vueItem.documentMeta.documentId;
          let name = params.name || params.title || "未命名应用";

          // example.html 会把数据直接拼进 <script> 里：
          //   template: `{{template}}`  → JS 模板字符串
          //   accountData: {{content}}  → JS 对象字面量
          // 内容中只要出现 script 结束标签或 HTML 注释起始符，HTML 解析器就会提前结束脚本块；
          // 出现反引号 / ${ 则会截断模板字符串。这里按上下文分别转义。
          let escapeHtmlText = function(value) {
            return String(value === undefined || value === null ? "" : value)
              .replace(/&/g, "&amp;")
              .replace(/</g, "&lt;")
              .replace(/>/g, "&gt;");
          };
          let escapeHtmlAttr = function(value) {
            return escapeHtmlText(value).replace(/"/g, "&quot;");
          };

          let content = params.content;
          if (typeof content !== "string") {
            content = JSON.stringify(content === undefined ? {} : content);
          }
          // JSON 里的 < 统一转成 \u003c：脚本块不会被提前闭合，运行时解析后仍是原来的 <
          let safeContent = content
            .replace(/</g, "\\u003c")
            .replace(/\u2028/g, "\\u2028")
            .replace(/\u2029/g, "\\u2029");

          let template =
            typeof params.template === "string" ? params.template : "";
          // 先转义反斜杠，再处理反引号 / ${ / <，保证运行时拿到的模板与原始内容一致
          let safeTemplate = template
            .replace(/\\/g, "\\\\")
            .replace(/`/g, "\\`")
            .replace(/\$\{/g, "\\${")
            .replace(/</g, "\\u003c");

          let exportParams = Object.assign({}, params, {
            name: escapeHtmlText(name),
            content: safeContent,
            template: safeTemplate,
            agentName: escapeHtmlText(document.title),
            creator: escapeHtmlText(
              webCpu.currentCard.task.vueItem.currentSettings.creator ||
              "匿名用户"
            ),
            createTime: escapeHtmlText(new Date().toLocaleString()),
            userName: escapeHtmlText(localStorage.userName || "匿名用户"),
            agentUrl: escapeHtmlAttr(
              location.origin + "/skill.html?solo=true&id=" + documentId
            ),
          });

          html = html.bindData(exportParams);
          name = name + ".html";
          WebTool.downloadString(html, name, "text/html");
        });
      },"dismissLoadingStatus":function() {
        this.loading = false;
        let elem = this.$refs.loadingEffectArea;
        if (!this.isSolo && !this.content.isSolo) {
          elem = this.$refs.resultComponentItem;
        }
        webCpu.CardItem.dismissMask(elem);
      },"switchLoadingStatus":function(str) {
        this.step = 1;
        this.loading = true;
        this.$nextTick(() => {
          let elem = this.$refs.loadingEffectArea;
          if (!this.isSolo && !this.content.isSolo) {
            elem = this.$refs.resultComponentItem;
          }
          let app = JSON.parse(JSON.stringify(this.loadingProgressApp));
          webCpu.CardItem.renderCardDialog(
            elem,
            app, {
              dialogName: "progressDialog",
              headerHidden: true,
              style: {
                background: "transparent",
              },
            }, {
              background: "transparent",
              marginTop: "-5px",
            }
          );
          this.currentTipsItem = app;
        });
      },"switchResultStatus":function(fullContent, flag) {
        if (!this.isStreaming) {
          return false;
        }
        this.dismissLoadingStatus();
        this.loading = false;
        this.step = 1;
        this.abortController = null;
        this.isStreaming = false;
        this.$nextTick(() => {
          let count = 0;
          try {
            let parsed = fullContent;
            if (typeof fullContent === "string") {
              parsed = WebTool.extractJSON(fullContent);
            }
            this.accountData = parsed;
            this.content.outputText = JSON.stringify(parsed);
            this.$nextTick(() => {
              if (!flag && this.dataEditable) {
                this.updateVersionList(this.content.outputText, () => {
                  this.updateResult(parsed);
                });
              } else {
                this.updateResult(parsed);
              }
            });
          } catch (e) {
            this.goBack(true);
            if (count < 5) {
              this.handleGenerate();
              this.$message({
                type: "warning",
                message: "生成的内容格式不正确，正在重试...",
              });
              count++;
            } else {
              this.$message({
                type: "warning",
                message: "生成的内容格式不正确，请稍后再试",
              });
            }
          }
        });
      },"goBack":function(flag) {
        if (flag) {
          this._goBack();
        } else {
          this.$confirm(
            "退出后无法恢复，确定退出吗？",
            webCpu.messages[lang].confirmDialogTitle, {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          ).then(() => {
            this._goBack();
          });
        }
      },"_goBack":function() {
        if (
          this.abortController &&
          typeof this.abortController.abort === "function"
        ) {
          this.abortController.abort();
        }
        this.step = 0;
        this.accountData = {};
        this.isStreaming = false;
        this.dismissLoadingStatus();
      },"updateResult":function(json) {
        let app = JSON.parse(JSON.stringify(this.currentAppItem));
        delete app.card;
        let elem = this.$refs.resultComponentItem;
        aigcInterface.updateResultView(json, app, elem);
      },"updateLoadingTips":function(result) {
        if (
          this.currentTipsItem &&
          this.currentTipsItem.card &&
          this.currentTipsItem.card.task
        ) {
          this.currentTipsItem.card.task.vueItem =
            this.currentTipsItem.card.task.vueItem || {};
          this.currentTipsItem.card.task.vueItem.result = result;
        }
      },"inputExampleText":function(item) {
        this.content.inputText = item.name || "";
        if (item.description) {
          this.content.inputText = item.name + ": " + item.description;
        }
      },"confirmGenerate":async function(query) {
        this.responseText = "";
        this.switchLoadingStatus();
        this.isStreaming = true;
        this.versionList = [];
        this.abortController = await aigcInterface.streamRequest(query, {
          immediateFinish: true,
          onMessage: (message) => {
            this.responseText = message;
            this.updateLoadingTips(message);
          },
          onStreamFinished: (result) => {
            this.switchResultStatus(result);
            this.content.outputText = result;
            this.versionList = [];
          },
          onError: (e) => {
            this.handleErrorCase();
          },
          onAction: (action) => {
            if (action === "stop") {
              this._goBack();
            } else if (action === "LOGIN") {
              aigcInterface.openLoginDialog(document.body, () => {
                this.confirmGenerate(query);
              });
              this.goBack(true);
            } else if (action === "RECHARGE") {
              aigcInterface.renderRechargeDialog(document.body);
              this.goBack(true);
            } else {}
          },
        });
      },"handleErrorCase":function() {
        this.$message({
          type: "warning",
          message: "请求出现异常，请重新尝试。",
        });
        this.goBack(true);
      },"handleGenerate":function() {
        if (!this.currentFile && !this.content.inputText) {
          this.$message({
            type: "warning",
            message: "需要输入具体需求描述。",
          });
          return false;
        }

        let query = this.query || {};
        query.context = this.content.inputText;
        query.documentId =
          query.documentId ||
          webCpu.currentCard.task.vueItem.documentMeta.documentId;
        if (this.currentFile) {
          WebTool.compressAndResizeBase64(
            this.currentFile,
            this.size,
            this.size,
            1
          ).then((base64) => {
            query.image = base64 || "";
            this.aigcRequest(query);
          });
        } else {
          if (location.host.includes("transweb.cn")) {
            this.confirmGenerate(query);
          } else {
            this.aigcRequest(query);
          }
        }
      },"aigcRequest":function(query, flag) {
        this.switchLoadingStatus();
        this.isStreaming = true;
        this.versionList = [];
        aigcInterface.requestPrompt(
          query.context,
          query.documentId,
          query,
          (content) => {
            this.responseText = content || "";
            this.content.outputText = this.responseText;
            this.switchResultStatus(this.responseText);
            this.versionList = [];
          },
          (e) => {
            this._goBack();
          }
        );
      },"modifyGenerate":function(query) {
        this.modifyLoading = true;
        query.documentId =
          query.documentId ||
          webCpu.currentCard.task.vueItem.documentMeta.documentId;
        query.baseContent = JSON.stringify(this.accountData);
        aigcInterface.requestPrompt(
          query.context,
          query.documentId,
          query,
          (content) => {
            this.modifyLoading = false;
            this.responseText = content || "";
            this.content.outputText = this.responseText;
            this.isStreaming = true;
            this.switchResultStatus(this.responseText);
          },
          (e) => {
            this.$message({
              type: "warning",
              message: "请求出现异常，请重新尝试。",
            });
            this.modifyLoading = false;
            this._goBack();
          }
        );
      }},"appMap":{"loginRegItem":{"url":"/mainApp/app/loginRegItem.js","key":"transweb_loginRegItem","cardName":"loginRegCard","dsl":{"data":{"position":"top","loginedMenu":[{"label":"⏱️ 历史记录","action":function(d, callback) {
                dialogMap.openDocumentApp(
                  webCpu.appConfig.aigcHistory, {
                    data: {
                      content: {
                        isSolo: true,
                      },
                      query: {
                        documentId: d.documentId || webCpu.currentVueItem.query.documentId,
                      },
                    },
                  }, {
                    title: "⏱️ 历史记录",
                    callback: callback,
                  }, {
                    width: "100%",
                    height: "100%",
                  }
                );
              }}]}}}},"mounted":async function() {
      this.content = this.content || {};
      this.adapter = webCpu.initAdapter(this.interface);
      let auto = WebTool.urlQuery(location.href, "auto");
      let caseUrl = WebTool.urlQuery(location.href, "caseUrl");
      let dataId = WebTool.urlQuery(location.href, "dataId");

      if (this.accountData && Object.keys(this.accountData).length !== 0) {
        this.isStreaming = true;
        this.switchResultStatus(this.accountData);
      } else if (caseUrl) {
        this.isStreaming = true;
        let d = await WebTool.fetchHtmlDataSmart(caseUrl);
        this.switchResultStatus(d.data);
      } else if (dataId) {
        aigcInterface.getDataItem(dataId, (result, tData) => {
          this.isStreaming = true;
          this.currentVersion = tData.versionId;
          this.dataEditable =
            tData.folderName + "" === localStorage.folderName &&
            !WebTool.urlQuery(location.href, "dataView");
          this.switchResultStatus(result);
        }, (err) => {
          this.$message({
            type: "warning",
            message: "获取数据失败，暂时没有权限访问。",
          });
        });
      } else {
        aigcInterface.handleAgentInput(location.href, (str) => {
          this.content.inputText = str;
          if (auto) {
            this.handleGenerate();
          }
        });
      }
    }};</script>