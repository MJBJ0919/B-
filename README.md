<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>B站视频后台数据</title>
    <style>
        :root {
            --primary-color: #fb7299;
            --secondary-color: #ff85ad;
            --error-color: #ff4d4f;
            --success-color: #52c41a;
            --warning-color: #faad14;
            --text-color: #333;
            --light-text: #666;
            --border-color: #ddd;
            --bg-color: #f5f5f5;
            --card-bg: #f9f9f9;
        }
        
        body {
            font-family: 'Microsoft YaHei', sans-serif;
            margin: 0;
            padding: 20px;
            background-color: var(--bg-color);
            color: var(--text-color);
        }
        
        .container {
            max-width: 1000px;
            margin: 0 auto;
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        
        h1 {
            color: var(--primary-color);
            text-align: center;
            margin-bottom: 30px;
        }
        
        .control-panel {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }
        
        .input-group {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        input {
            flex: 1;
            padding: 10px 12px;
            border: 1px solid var(--border-color);
            border-radius: 4px;
            font-size: 14px;
            min-width: 120px;
        }
        
        button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 10px 16px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.2s;
            white-space: nowrap;
        }
        
        button:hover:not(:disabled) {
            background-color: var(--secondary-color);
            transform: translateY(-1px);
        }
        
        button:disabled {
            background-color: #ccc;
            cursor: not-allowed;
            transform: none;
        }
        
        .refresh-controls {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .refresh-timer {
            font-family: monospace;
            color: var(--light-text);
            min-width: 40px;
            text-align: center;
        }
        
        .video-info {
            margin-top: 30px;
        }
        
        .video-header {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-bottom: 20px;
        }
        
        .video-cover {
            width: 320px;
            height: 180px;
            object-fit: cover;
            border-radius: 4px;
            margin-bottom: 15px;
        }
        
        .video-title {
            font-size: 20px;
            font-weight: bold;
            text-align: center;
            margin-bottom: 5px;
            word-break: break-word;
            max-width: 100%;
        }
        
        .video-meta {
            color: var(--light-text);
            text-align: center;
            margin-bottom: 20px;
        }
        
        .time-info {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }
        
        .time-card {
            background-color: var(--card-bg);
            padding: 15px;
            border-radius: 4px;
        }
        
        .time-title {
            color: var(--light-text);
            margin-bottom: 5px;
            font-size: 14px;
        }
        
        .time-value {
            font-size: 16px;
            font-family: monospace;
        }
        
        .target-achieved {
            color: var(--warning-color);
            font-weight: bold;
        }
        
        .stats-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }
        
        .stat-card {
            background-color: var(--card-bg);
            padding: 15px;
            border-radius: 4px;
        }
        
        .stat-title {
            color: var(--light-text);
            margin-bottom: 5px;
            font-size: 14px;
        }
        
        .stat-value {
            font-size: 24px;
            font-weight: bold;
            display: flex;
            align-items: baseline;
        }
        
        .stat-delta {
            font-size: 14px;
            margin-left: 5px;
            color: var(--success-color);
        }
        
        .error-message {
            color: var(--error-color);
            margin-top: 10px;
            padding: 10px;
            background-color: #fff2f0;
            border-radius: 4px;
            display: none;
        }
        
        .success-message {
            color: var(--success-color);
            margin-top: 10px;
            padding: 10px;
            background-color: #f6ffed;
            border-radius: 4px;
            display: none;
        }
        
        .loading {
            display: none;
            text-align: center;
            margin: 20px 0;
            color: var(--light-text);
        }
        
        .loading-spinner {
            border: 3px solid #f3f3f3;
            border-top: 3px solid var(--primary-color);
            border-radius: 50%;
            width: 20px;
            height: 20px;
            animation: spin 1s linear infinite;
            display: inline-block;
            margin-right: 10px;
        }
        
        .history-container {
            margin-top: 30px;
            display: none;
        }
        
        .history-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .history-pagination {
            display: flex;
            justify-content: center;
            margin-top: 20px;
            gap: 10px;
        }
        
        .history-page-btn {
            padding: 6px 12px;
            min-width: 80px;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        @media (max-width: 768px) {
            .control-panel {
                grid-template-columns: 1fr;
            }
            
            .input-group {
                flex-direction: column;
                align-items: stretch;
            }
            
            .refresh-controls {
                flex-wrap: wrap;
            }
            
            .video-cover {
                width: 100%;
                height: auto;
                max-height: 200px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>B站视频后台数据</h1>
        
        <div class="control-panel">
            <div class="input-group">
                <input type="text" id="video-bvid" placeholder="输入BV号（如BV1ih411b7La）">
                <button id="fetch-btn">开始监控</button>
            </div>
            
            <div class="input-group">
                <input type="number" id="target-views" placeholder="目标播放量（可选）">
                <button id="set-target-btn">设置目标</button>
            </div>
            
            <div class="input-group">
                <input type="number" id="refresh-interval" value="30" min="5" placeholder="自动刷新间隔（秒）">
                <button id="set-interval-btn">设置间隔</button>
            </div>
            
            <div class="refresh-controls">
                <span class="refresh-timer" id="refresh-timer">--</span>
                <button id="manual-refresh-btn">手动刷新</button>
            </div>
        </div>
        
        <div class="error-message" id="error-message"></div>
        <div class="success-message" id="success-message"></div>
        
        <div class="loading" id="loading">
            <div class="loading-spinner"></div>
            正在获取数据...
        </div>
        
        <div class="video-info" id="video-info">
            <div class="video-header">
                <img class="video-cover" id="video-cover" src="">
                <h2 class="video-title" id="video-title"></h2>
                <div class="video-meta">
                    <span id="video-author"></span>
                    <span> · </span>
                    <span id="video-pubdate"></span>
                </div>
            </div>
            
            <div class="time-info">
                <div class="time-card">
                    <div class="time-title">发布时间</div>
                    <div class="time-value" id="publish-time"></div>
                </div>
                <div class="time-card">
                    <div class="time-title">当前时间</div>
                    <div class="time-value" id="current-time"></div>
                </div>
                <div class="time-card">
                    <div class="time-title">已发布时长</div>
                    <div class="time-value" id="published-duration"></div>
                </div>
                <div class="time-card" id="target-container" style="display: none;">
                    <div class="time-title">目标播放量</div>
                    <div class="time-value" id="target-views-value"></div>
                </div>
                <div class="time-card" id="target-achieved-container" style="display: none;">
                    <div class="time-title">目标达成时间</div>
                    <div class="time-value target-achieved" id="target-achieved-time"></div>
                </div>
            </div>
            
            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-title">播放量</div>
                    <div class="stat-value" id="play-count">--</div>
                </div>
                <div class="stat-card">
                    <div class="stat-title">点赞数</div>
                    <div class="stat-value" id="like-count">--</div>
                </div>
                <div class="stat-card">
                    <div class="stat-title">硬币数</div>
                    <div class="stat-value" id="coin-count">--</div>
                </div>
                <div class="stat-card">
                    <div class="stat-title">收藏数</div>
                    <div class="stat-value" id="favorite-count">--</div>
                </div>
                <div class="stat-card">
                    <div class="stat-title">分享数</div>
                    <div class="stat-value" id="share-count">--</div>
                </div>
                <div class="stat-card">
                    <div class="stat-title">弹幕数</div>
                    <div class="stat-value" id="danmaku-count">--</div>
                </div>
                <div class="stat-card">
                    <div class="stat-title">评论数</div>
                    <div class="stat-value" id="reply-count">--</div>
                </div>
            </div>
        </div>
        
        <div class="history-container" id="history-container">
            <div class="history-header">
                <h3>历史记录</h3>
                <div id="history-pagination-info">第 1 页 / 共 1 页</div>
            </div>
            <div id="history-content"></div>
            <div class="history-pagination" id="history-pagination"></div>
        </div>
    </div>

    <script>
        // 全局变量
        let currentBvid = '';
        let currentAid = '';
        let refreshInterval = 30; // 默认30秒
        let refreshTimer = null;
        let countdownTimer = null;
        let lastData = null;
        let historyData = [];
        let currentHistoryPage = 1;
        const itemsPerPage = 1; // 每页显示1条记录
        let targetViews = null;
        let targetAchieved = false;
        let targetAchievedTime = null;
        
        // DOM元素
        const fetchBtn = document.getElementById('fetch-btn');
        const manualRefreshBtn = document.getElementById('manual-refresh-btn');
        const setIntervalBtn = document.getElementById('set-interval-btn');
        const setTargetBtn = document.getElementById('set-target-btn');
        const videoBvidInput = document.getElementById('video-bvid');
        const refreshIntervalInput = document.getElementById('refresh-interval');
        const targetViewsInput = document.getElementById('target-views');
        const errorMessage = document.getElementById('error-message');
        const successMessage = document.getElementById('success-message');
        const loading = document.getElementById('loading');
        const videoInfo = document.getElementById('video-info');
        const refreshTimerDisplay = document.getElementById('refresh-timer');
        const historyContainer = document.getElementById('history-container');
        const historyContent = document.getElementById('history-content');
        const historyPaginationInfo = document.getElementById('history-pagination-info');
        const historyPagination = document.getElementById('history-pagination');
        
        // 初始化
        document.addEventListener('DOMContentLoaded', function() {
            fetchBtn.addEventListener('click', startMonitoring);
            manualRefreshBtn.addEventListener('click', manualRefresh);
            setIntervalBtn.addEventListener('click', setRefreshInterval);
            setTargetBtn.addEventListener('click', setTargetViews);
            
            // 初始化时间显示
            updateCurrentTime();
            setInterval(updateCurrentTime, 1000);
        });
        
        // 开始监控
        async function startMonitoring() {
            const bvid = videoBvidInput.value.trim();
            
            if (!bvid) {
                showError('请输入BV号');
                return;
            }
            
            currentBvid = bvid.startsWith('BV') ? bvid : 'BV' + bvid;
            videoBvidInput.value = currentBvid;
            
            showLoading(true);
            hideError();
            hideSuccess();
            hideVideoInfo();
            
            try {
                // 获取视频数据
                await fetchVideoData();
                
                // 启动自动刷新
                startAutoRefresh();
                
            } catch (error) {
                console.error('启动监控失败:', error);
                showError(`启动监控失败: ${error.message}`);
            } finally {
                showLoading(false);
            }
        }
        
        // 获取视频数据
        async function fetchVideoData() {
            try {
                // 获取视频基本信息
                const videoData = await fetchBilibiliData(currentBvid);
                currentAid = videoData.aid;
                
                // 获取评论数
                const replyCount = await fetchReplyCount(currentAid);
                videoData.stat.reply = replyCount;
                
                // 显示数据
                displayVideoInfo(videoData);
                
                // 检查目标播放量
                checkTargetViews(videoData.stat.view);
                
                // 保存上一次数据用于计算增量
                if (lastData) {
                    calculateDeltas(videoData.stat, lastData.stat);
                }
                lastData = videoData;
                
                // 添加到历史记录
                addToHistory(videoData);
                
            } catch (error) {
                console.error('获取数据失败:', error);
                throw error;
            }
        }
        
        // 获取B站视频数据
        async function fetchBilibiliData(bvid) {
            const proxyUrl = `https://api.allorigins.win/get?url=${encodeURIComponent(
                `https://api.bilibili.com/x/web-interface/view?bvid=${bvid}`
            )}`;
            
            const response = await fetch(proxyUrl);
            if (!response.ok) throw new Error('网络请求失败');
            
            const data = await response.json();
            const apiData = JSON.parse(data.contents);
            
            if (apiData.code !== 0) throw new Error(apiData.message || '获取数据失败');
            
            return apiData.data;
        }
        
        // 获取评论数
        async function fetchReplyCount(aid) {
            const proxyUrl = `https://api.allorigins.win/get?url=${encodeURIComponent(
                `https://api.bilibili.com/x/v2/reply/count?oid=${aid}&type=1`
            )}`;
            
            const response = await fetch(proxyUrl);
            if (!response.ok) throw new Error('获取评论数失败');
            
            const data = await response.json();
            const apiData = JSON.parse(data.contents);
            
            if (apiData.code !== 0) throw new Error(apiData.message || '获取评论数失败');
            
            return apiData.data.count;
        }
        
        // 显示视频信息
        function displayVideoInfo(data) {
            // 基本信息
            document.getElementById('video-title').textContent = data.title;
            document.getElementById('video-cover').src = data.pic;
            document.getElementById('video-author').textContent = `UP主: ${data.owner.name}`;
            
            // 发布时间
            const pubDate = new Date(data.pubdate * 1000);
            document.getElementById('publish-time').textContent = formatFullDate(pubDate);
            document.getElementById('video-pubdate').textContent = formatDate(pubDate);
            
            // 统计数据
            document.getElementById('play-count').textContent = formatNumber(data.stat.view);
            document.getElementById('like-count').textContent = formatNumber(data.stat.like);
            document.getElementById('coin-count').textContent = formatNumber(data.stat.coin);
            document.getElementById('favorite-count').textContent = formatNumber(data.stat.favorite);
            document.getElementById('share-count').textContent = formatNumber(data.stat.share);
            document.getElementById('danmaku-count').textContent = formatNumber(data.stat.danmaku);
            document.getElementById('reply-count').textContent = formatNumber(data.stat.reply);
            
            // 更新发布时间信息
            updatePublishedDuration(pubDate);
            
            // 显示目标容器
            if (targetViews !== null) {
                document.getElementById('target-container').style.display = 'block';
                document.getElementById('target-views-value').textContent = formatNumber(targetViews);
                
                if (targetAchieved) {
                    document.getElementById('target-achieved-container').style.display = 'block';
                    document.getElementById('target-achieved-time').textContent = formatFullDate(targetAchievedTime);
                } else {
                    document.getElementById('target-achieved-container').style.display = 'none';
                }
            } else {
                document.getElementById('target-container').style.display = 'none';
                document.getElementById('target-achieved-container').style.display = 'none';
            }
            
            showVideoInfo();
        }
        
        // 计算增量
        function calculateDeltas(currentStats, lastStats) {
            const stats = [
                { id: 'play-count', value: currentStats.view, last: lastStats.view },
                { id: 'like-count', value: currentStats.like, last: lastStats.like },
                { id: 'coin-count', value: currentStats.coin, last: lastStats.coin },
                { id: 'favorite-count', value: currentStats.favorite, last: lastStats.favorite },
                { id: 'share-count', value: currentStats.share, last: lastStats.share },
                { id: 'danmaku-count', value: currentStats.danmaku, last: lastStats.danmaku },
                { id: 'reply-count', value: currentStats.reply, last: (lastStats.reply || 0) }
            ];
            
            stats.forEach(stat => {
                const delta = stat.value - stat.last;
                if (delta > 0) {
                    const element = document.getElementById(stat.id);
                    const deltaSpan = document.createElement('span');
                    deltaSpan.className = 'stat-delta';
                    deltaSpan.textContent = `(+${formatNumber(delta)})`;
                    
                    // 移除旧的增量显示
                    const oldDelta = element.querySelector('.stat-delta');
                    if (oldDelta) element.removeChild(oldDelta);
                    
                    element.appendChild(deltaSpan);
                }
            });
        }
        
        // 设置自动刷新间隔
        function setRefreshInterval() {
            const interval = parseInt(refreshIntervalInput.value);
            
            if (isNaN(interval) || interval < 5) {
                showError('刷新间隔不能小于5秒');
                return;
            }
            
            refreshInterval = interval;
            showSuccess(`自动刷新间隔已设置为 ${interval} 秒`);
            
            // 如果已经在监控中，重新启动定时器
            if (currentBvid) {
                clearInterval(refreshTimer);
                startAutoRefresh();
            }
        }
        
        // 开始自动刷新
        function startAutoRefresh() {
            // 清除现有定时器
            clearInterval(refreshTimer);
            clearInterval(countdownTimer);
            
            // 立即开始倒计时
            startCountdown();
            
            // 设置定时刷新
            refreshTimer = setInterval(() => {
                fetchVideoData().catch(error => {
                    console.error('自动刷新失败:', error);
                    showError(`自动刷新失败: ${error.message}`);
                });
                startCountdown();
            }, refreshInterval * 1000);
        }
        
        // 开始倒计时
        function startCountdown() {
            let seconds = refreshInterval;
            updateRefreshTimerDisplay(seconds);
            
            clearInterval(countdownTimer);
            countdownTimer = setInterval(() => {
                seconds--;
                updateRefreshTimerDisplay(seconds);
                
                if (seconds <= 0) {
                    clearInterval(countdownTimer);
                }
            }, 1000);
        }
        
        // 更新刷新计时器显示
        function updateRefreshTimerDisplay(seconds) {
            refreshTimerDisplay.textContent = `${seconds}s`;
        }
        
        // 手动刷新
        function manualRefresh() {
            if (manualRefreshBtn.disabled) return;
            
            // 禁用按钮并开始冷却
            manualRefreshBtn.disabled = true;
            manualRefreshBtn.textContent = '冷却中...';
            
            // 重置自动刷新间隔为30秒
            refreshInterval = 30;
            refreshIntervalInput.value = '30';
            
            // 立即刷新数据
            fetchVideoData().catch(error => {
                console.error('手动刷新失败:', error);
                showError(`手动刷新失败: ${error.message}`);
            });
            
            // 重新启动自动刷新
            clearInterval(refreshTimer);
            startAutoRefresh();
            
            // 冷却倒计时
            let cooldown = 5;
            const cooldownInterval = setInterval(() => {
                cooldown--;
                manualRefreshBtn.textContent = `冷却中...(${cooldown}s)`;
                
                if (cooldown <= 0) {
                    clearInterval(cooldownInterval);
                    manualRefreshBtn.disabled = false;
                    manualRefreshBtn.textContent = '手动刷新';
                }
            }, 1000);
        }
        
        // 设置目标播放量
        function setTargetViews() {
            const target = parseInt(targetViewsInput.value);
            
            if (isNaN(target) || target <= 0) {
                showError('请输入有效的目标播放量');
                return;
            }
            
            targetViews = target;
            targetViewsInput.value = target; // 保留输入值
            targetAchieved = false;
            targetAchievedTime = null;
            
            document.getElementById('target-container').style.display = 'block';
            document.getElementById('target-views-value').textContent = formatNumber(target);
            document.getElementById('target-achieved-container').style.display = 'none';
            
            showSuccess('目标播放量设置成功');
            
            // 如果已经有当前数据，检查是否已经达到目标
            if (lastData) {
                checkTargetViews(lastData.stat.view);
            }
        }
        
        // 检查目标播放量
        function checkTargetViews(currentViews) {
            if (!targetViews || targetAchieved) return;
            
            if (currentViews >= targetViews) {
                targetAchieved = true;
                targetAchievedTime = new Date();
                document.getElementById('target-achieved-container').style.display = 'block';
                document.getElementById('target-achieved-time').textContent = formatFullDate(targetAchievedTime);
                showSuccess(`目标播放量 ${formatNumber(targetViews)} 已达成！`);
            }
        }
        
        // 添加到历史记录
        function addToHistory(data) {
            const historyItem = {
                timestamp: new Date(),
                data: {
                    title: data.title,
                    pic: data.pic,
                    owner: data.owner,
                    pubdate: data.pubdate,
                    stat: data.stat
                }
            };
            
            historyData.unshift(historyItem);
            updateHistoryDisplay();
        }
        
        // 更新历史记录显示
        function updateHistoryDisplay() {
            if (historyData.length === 0) {
                historyContainer.style.display = 'none';
                return;
            }
            
            historyContainer.style.display = 'block';
            
            // 计算分页
            const totalPages = Math.ceil(historyData.length / itemsPerPage);
            const startIndex = (currentHistoryPage - 1) * itemsPerPage;
            const endIndex = startIndex + itemsPerPage;
            const paginatedData = historyData.slice(startIndex, endIndex);
            
            // 显示当前页数据
            historyContent.innerHTML = '';
            
            paginatedData.forEach(item => {
                const historyItem = document.createElement('div');
                historyItem.className = 'video-info';
                historyItem.style.marginBottom = '30px';
                
                const pubDate = new Date(item.data.pubdate * 1000);
                const recordDate = new Date(item.timestamp);
                
                historyItem.innerHTML = `
                    <div class="video-header">
                        <img class="video-cover" src="${item.data.pic}" style="width: 160px; height: 90px;">
                        <div>
                            <h2 class="video-title">${item.data.title}</h2>
                            <div class="video-meta">
                                <span>UP主: ${item.data.owner.name}</span>
                                <span> · </span>
                                <span>${formatDate(pubDate)}</span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="time-info">
                        <div class="time-card">
                            <div class="time-title">记录时间</div>
                            <div class="time-value">${formatFullDate(recordDate)}</div>
                        </div>
                        <div class="time-card">
                            <div class="time-title">已发布时长</div>
                            <div class="time-value">${formatDuration(pubDate, recordDate)}</div>
                        </div>
                    </div>
                    
                    <div class="stats-container">
                        <div class="stat-card">
                            <div class="stat-title">播放量</div>
                            <div class="stat-value">${formatNumber(item.data.stat.view)}</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-title">点赞数</div>
                            <div class="stat-value">${formatNumber(item.data.stat.like)}</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-title">硬币数</div>
                            <div class="stat-value">${formatNumber(item.data.stat.coin)}</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-title">收藏数</div>
                            <div class="stat-value">${formatNumber(item.data.stat.favorite)}</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-title">分享数</div>
                            <div class="stat-value">${formatNumber(item.data.stat.share)}</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-title">弹幕数</div>
                            <div class="stat-value">${formatNumber(item.data.stat.danmaku)}</div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-title">评论数</div>
                            <div class="stat-value">${formatNumber(item.data.stat.reply)}</div>
                        </div>
                    </div>
                `;
                
                historyContent.appendChild(historyItem);
            });
            
            // 更新分页信息
            historyPaginationInfo.textContent = `第 ${currentHistoryPage} 页 / 共 ${totalPages} 页`;
            
            // 更新分页控件
            historyPagination.innerHTML = '';
            
            if (currentHistoryPage > 1) {
                const prevBtn = document.createElement('button');
                prevBtn.className = 'history-page-btn';
                prevBtn.textContent = '上一页';
                prevBtn.addEventListener('click', () => {
                    currentHistoryPage--;
                    updateHistoryDisplay();
                });
                historyPagination.appendChild(prevBtn);
            }
            
            if (currentHistoryPage < totalPages) {
                const nextBtn = document.createElement('button');
                nextBtn.className = 'history-page-btn';
                nextBtn.textContent = '下一页';
                nextBtn.addEventListener('click', () => {
                    currentHistoryPage++;
                    updateHistoryDisplay();
                });
                historyPagination.appendChild(nextBtn);
            }
        }
        
        // 更新当前时间
        function updateCurrentTime() {
            const now = new Date();
            document.getElementById('current-time').textContent = formatFullDate(now);
            
            // 如果视频已加载，更新已发布时长
            if (lastData) {
                const pubDate = new Date(lastData.pubdate * 1000);
                updatePublishedDuration(pubDate);
            }
        }
        
        // 更新已发布时长
        function updatePublishedDuration(pubDate) {
            const now = new Date();
            document.getElementById('published-duration').textContent = formatDuration(pubDate, now);
        }
        
        // 格式化日期 (YYYY-MM-DD)
        function formatDate(date) {
            return date.toLocaleDateString('zh-CN', {
                year: 'numeric',
                month: '2-digit',
                day: '2-digit'
            });
        }
        
        // 格式化完整日期 (YYYY-MM-DD HH:MM:SS)
        function formatFullDate(date) {
            return date.toLocaleString('zh-CN', {
                year: 'numeric',
                month: '2-digit',
                day: '2-digit',
                hour: '2-digit',
                minute: '2-digit',
                second: '2-digit',
                hour12: false
            }).replace(/\//g, '-');
        }
        
        // 格式化时长 (X年X月X日 X小时X分钟X秒)
        function formatDuration(startDate, endDate) {
            let seconds = Math.floor((endDate - startDate) / 1000);
            
            const years = Math.floor(seconds / (365 * 24 * 60 * 60));
            seconds -= years * 365 * 24 * 60 * 60;
            
            const months = Math.floor(seconds / (30 * 24 * 60 * 60));
            seconds -= months * 30 * 24 * 60 * 60;
            
            const days = Math.floor(seconds / (24 * 60 * 60));
            seconds -= days * 24 * 60 * 60;
            
            const hours = Math.floor(seconds / (60 * 60));
            seconds -= hours * 60 * 60;
            
            const minutes = Math.floor(seconds / 60);
            seconds -= minutes * 60;
            
            let duration = '';
            if (years > 0) duration += `${years}年`;
            if (months > 0) duration += `${months}月`;
            if (days > 0) duration += `${days}日`;
            if (hours > 0) duration += `${hours}小时`;
            if (minutes > 0) duration += `${minutes}分钟`;
            if (seconds > 0 || duration === '') duration += `${seconds}秒`;
            
            return duration;
        }
        
        // 格式化数字 (添加千位分隔符)
        function formatNumber(num) {
            return num.toLocaleString('zh-CN');
        }
        
        // 显示加载状态
        function showLoading(show) {
            loading.style.display = show ? 'block' : 'none';
            fetchBtn.disabled = show;
        }
        
        // 显示错误信息
        function showError(message) {
            errorMessage.textContent = message;
            errorMessage.style.display = 'block';
            setTimeout(() => {
                errorMessage.style.display = 'none';
            }, 5000);
        }
        
        // 隐藏错误信息
        function hideError() {
            errorMessage.style.display = 'none';
        }
        
        // 显示成功信息
        function showSuccess(message) {
            successMessage.textContent = message;
            successMessage.style.display = 'block';
            setTimeout(() => {
                successMessage.style.display = 'none';
            }, 3000);
        }
        
        // 隐藏成功信息
        function hideSuccess() {
            successMessage.style.display = 'none';
        }
        
        // 显示视频信息
        function showVideoInfo() {
            videoInfo.style.display = 'block';
        }
        
        // 隐藏视频信息
        function hideVideoInfo() {
            videoInfo.style.display = 'none';
        }
    </script>
</body>
</html>
