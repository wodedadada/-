<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>分类抽签小程序</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#3B82F6',
                        secondary: '#F97316',
                        neutral: '#F3F4F6',
                        category1: '#F87171', // 假号：红色系
                        category2: '#FBBF24', // 未实名真号：黄色系
                        category3: '#10B981', // 实名真号：绿色系
                    },
                    fontFamily: {
                        sans: ['Inter', 'system-ui', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .text-shadow {
                text-shadow: 0 2px 4px rgba(0,0,0,0.1);
            }
            .card-hover {
                @apply transition-all duration-300 hover:shadow-lg hover:-translate-y-1;
            }
            .btn-category1 {
                @apply bg-category1 text-white font-medium py-2 px-4 rounded-lg shadow transition-all duration-300 hover:bg-category1/90 hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-category1/50;
            }
            .btn-category2 {
                @apply bg-category2 text-white font-medium py-2 px-4 rounded-lg shadow transition-all duration-300 hover:bg-category2/90 hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-category2/50;
            }
            .btn-category3 {
                @apply bg-category3 text-white font-medium py-2 px-4 rounded-lg shadow transition-all duration-300 hover:bg-category3/90 hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-category3/50;
            }
            .input-field {
                @apply w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-primary/50 focus:border-primary transition-all duration-200;
            }
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">
    <header class="bg-gradient-to-r from-primary to-primary/80 text-white shadow-md">
        <div class="container mx-auto px-4 py-6">
            <h1 class="text-[clamp(1.8rem,4vw,2.5rem)] font-bold text-shadow flex items-center">
                <i class="fa fa-random mr-3"></i>分类抽签小程序
            </h1>
            <p class="mt-2 text-white/90 max-w-2xl">
                支持三类名单分类：假号、未实名真号、实名真号，按分类独立抽奖
            </p>
        </div>
    </header>

    <main class="container mx-auto px-4 py-8">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <!-- 左侧：提交名字区域 -->
            <div class="lg:col-span-1">
                <div class="bg-white rounded-xl shadow-md p-6 card-hover">
                    <h2 class="text-xl font-bold text-gray-800 mb-4 flex items-center">
                        <i class="fa fa-user-plus text-primary mr-2"></i>提交名字
                    </h2>
                    <form id="nameForm" class="space-y-4">
                        <div>
                            <label for="contributorName" class="block text-sm font-medium text-gray-700 mb-1">
                                您的称呼
                            </label>
                            <input type="text" id="contributorName" placeholder="请输入您的称呼" required
                                class="input-field">
                        </div>
                        <div>
                            <label for="personName" class="block text-sm font-medium text-gray-700 mb-1">
                                参与人姓名
                            </label>
                            <input type="text" id="personName" placeholder="请输入参与人姓名" required
                                class="input-field">
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">
                                名单分类
                            </label>
                            <div class="grid grid-cols-3 gap-2">
                                <label class="flex items-center justify-center p-3 bg-category1/10 rounded-lg cursor-pointer hover:bg-category1/20 transition-colors">
                                    <input type="radio" name="category" value="1" class="mr-2" checked>
                                    <span class="text-category1 text-sm">假号</span>
                                </label>
                                <label class="flex items-center justify-center p-3 bg-category2/10 rounded-lg cursor-pointer hover:bg-category2/20 transition-colors">
                                    <input type="radio" name="category" value="2" class="mr-2">
                                    <span class="text-category2 text-sm">未实名真号</span>
                                </label>
                                <label class="flex items-center justify-center p-3 bg-category3/10 rounded-lg cursor-pointer hover:bg-category3/20 transition-colors">
                                    <input type="radio" name="category" value="3" class="mr-2">
                                    <span class="text-category3 text-sm">实名真号</span>
                                </label>
                            </div>
                        </div>
                        <button type="submit" class="btn-primary w-full flex justify-center items-center">
                            <i class="fa fa-plus-circle mr-2"></i>添加到名单
                        </button>
                    </form>
                </div>
                
                <!-- 分类名单和抽奖机会 -->
                <div class="mt-6 space-y-6">
                    <!-- 假号名单 -->
                    <div class="bg-white rounded-xl shadow-md p-6 card-hover">
                        <h3 class="text-lg font-semibold text-gray-800 mb-3 flex items-center">
                            <i class="fa fa-list-ul text-category1 mr-2"></i>假号名单
                            <span id="category1Count" class="ml-2 text-sm bg-category1/10 text-category1 px-2 py-0.5 rounded-full">0个</span>
                        </h3>
                        <div id="category1Items" class="divide-y divide-gray-100 max-h-32 overflow-y-auto">
                            <div class="text-gray-500 text-center py-2">暂无提交的名字</div>
                        </div>
                        <div class="mt-3 p-2 bg-category1/5 rounded-lg">
                            <p class="text-sm text-gray-700">
                                抽奖机会: <span id="category1Chances" class="font-bold text-category1">0</span>
                            </p>
                        </div>
                    </div>
                    
                    <!-- 未实名真号名单 -->
                    <div class="bg-white rounded-xl shadow-md p-6 card-hover">
                        <h3 class="text-lg font-semibold text-gray-800 mb-3 flex items-center">
                            <i class="fa fa-list-ul text-category2 mr-2"></i>未实名真号名单
                            <span id="category2Count" class="ml-2 text-sm bg-category2/10 text-category2 px-2 py-0.5 rounded-full">0个</span>
                        </h3>
                        <div id="category2Items" class="divide-y divide-gray-100 max-h-32 overflow-y-auto">
                            <div class="text-gray-500 text-center py-2">暂无提交的名字</div>
                        </div>
                        <div class="mt-3 p-2 bg-category2/5 rounded-lg">
                            <p class="text-sm text-gray-700">
                                抽奖机会: <span id="category2Chances" class="font-bold text-category2">0</span>
                            </p>
                        </div>
                    </div>
                    
                    <!-- 实名真号名单 -->
                    <div class="bg-white rounded-xl shadow-md p-6 card-hover">
                        <h3 class="text-lg font-semibold text-gray-800 mb-3 flex items-center">
                            <i class="fa fa-list-ul text-category3 mr-2"></i>实名真号名单
                            <span id="category3Count" class="ml-2 text-sm bg-category3/10 text-category3 px-2 py-0.5 rounded-full">0个</span>
                        </h3>
                        <div id="category3Items" class="divide-y divide-gray-100 max-h-32 overflow-y-auto">
                            <div class="text-gray-500 text-center py-2">暂无提交的名字</div>
                        </div>
                        <div class="mt-3 p-2 bg-category3/5 rounded-lg">
                            <p class="text-sm text-gray-700">
                                抽奖机会: <span id="category3Chances" class="font-bold text-category3">0</span>
                            </p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 右侧：抽奖区域 -->
            <div class="lg:col-span-2">
                <div class="bg-white rounded-xl shadow-md p-6 card-hover">
                    <h2 class="text-xl font-bold text-gray-800 mb-6 flex items-center">
                        <i class="fa fa-gift text-secondary mr-2"></i>开始抽奖
                    </h2>
                    
                    <!-- 抽奖类型选择 -->
                    <div class="grid grid-cols-3 gap-4 mb-8">
                        <button id="drawCategory1Btn" class="btn-category1 p-4 rounded-lg flex flex-col items-center">
                            <i class="fa fa-ticket text-xl mb-1"></i>
                            <span class="text-sm">假号抽奖</span>
                            <span id="drawCategory1Count" class="text-xs mt-1 bg-white/30 px-2 py-0.5 rounded-full">0次机会</span>
                        </button>
                        <button id="drawCategory2Btn" class="btn-category2 p-4 rounded-lg flex flex-col items-center">
                            <i class="fa fa-ticket text-xl mb-1"></i>
                            <span class="text-sm">未实名真号抽奖</span>
                            <span id="drawCategory2Count" class="text-xs mt-1 bg-white/30 px-2 py-0.5 rounded-full">0次机会</span>
                        </button>
                        <button id="drawCategory3Btn" class="btn-category3 p-4 rounded-lg flex flex-col items-center">
                            <i class="fa fa-ticket text-xl mb-1"></i>
                            <span class="text-sm">实名真号抽奖</span>
                            <span id="drawCategory3Count" class="text-xs mt-1 bg-white/30 px-2 py-0.5 rounded-full">0次机会</span>
                        </button>
                    </div>
                    
                    <!-- 抽奖结果区域 -->
                    <div id="lotteryResult" class="hidden">
                        <div class="bg-gray-50 rounded-xl p-6 mb-4">
                            <h3 class="text-xl font-bold text-gray-800 mb-3">
                                <span id="resultCategory" class="px-3 py-1 rounded-full text-sm">假号</span>
                                抽奖结果
                            </h3>
                            <div class="text-center py-6">
                                <div class="inline-block p-4 bg-white rounded-full shadow-lg">
                                    <span id="winnerName" class="text-3xl font-bold">--</span>
                                </div>
                                <p class="mt-4 text-gray-600">
                                    剩余 <span id="remainingChances" class="font-bold">0</span> 次抽奖机会
                                </p>
                            </div>
                        </div>
                        <div class="flex justify-center">
                            <button id="drawAgainBtn" class="btn-secondary flex items-center">
                                <i class="fa fa-refresh mr-2"></i>再抽一次
                            </button>
                        </div>
                    </div>
                    
                    <!-- 初始状态提示 -->
                    <div id="initialStatus" class="text-center py-12">
                        <div class="text-gray-500 mb-4">
                            <i class="fa fa-hand-pointer-o text-4xl mb-3"></i>
                            <p>请先提交名字以获得抽奖机会</p>
                        </div>
                    </div>
                    
                    <!-- 全部参与人列表 -->
                    <div class="mt-8">
                        <h3 class="text-lg font-semibold text-gray-700 mb-3 flex items-center">
                            <i class="fa fa-users text-primary mr-2"></i>全部参与人名单
                            <span id="totalParticipantsCount" class="ml-2 text-sm bg-primary/10 text-primary px-2 py-0.5 rounded-full">0个</span>
                        </h3>
                        <div id="allParticipantsList" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-2 max-h-80 overflow-y-auto">
                            <div class="col-span-full text-gray-500 text-center py-4">暂无参与人</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>

    <footer class="bg-gray-800 text-white mt-12 py-6">
        <div class="container mx-auto px-4">
            <div class="flex flex-col md:flex-row justify-between items-center">
                <div class="mb-4 md:mb-0">
                    <p class="text-gray-400">© 2025 分类抽签小程序 - 公平、透明的分类抽签系统</p>
                </div>
                <div class="flex space-x-4">
                    <a href="#" class="text-gray-400 hover:text-white transition-colors">
                        <i class="fa fa-question-circle"></i> 帮助
                    </a>
                    <a href="#" class="text-gray-400 hover:text-white transition-colors">
                        <i class="fa fa-info-circle"></i> 关于
                    </a>
                </div>
            </div>
        </div>
    </footer>

    <script>
        // 数据存储
        let participants = [];
        let mySubmissions = {
            1: [], // 假号
            2: [], // 未实名真号
            3: [], // 实名真号
        };
        let myChances = {
            1: 0, // 假号抽奖机会
            2: 0, // 未实名真号抽奖机会
            3: 0, // 实名真号抽奖机会
        };
        let contributorName = '';
        let currentCategory = 1; // 当前选中的抽奖分类
        let useLocalStorage = true;

        // 数据存储键名
        const STORAGE_KEYS = {
            PARTICIPANTS: 'lotteryParticipants',
            MY_SUBMISSIONS: 'lotteryMySubmissions',
            MY_CHANCES: 'lotteryMyChances',
            CONTRIBUTOR_NAME: 'lotteryContributorName'
        };

        // DOM 元素
        const nameForm = document.getElementById('nameForm');
        const contributorNameInput = document.getElementById('contributorName');
        const personNameInput = document.getElementById('personName');
        const category1Items = document.getElementById('category1Items');
        const category2Items = document.getElementById('category2Items');
        const category3Items = document.getElementById('category3Items');
        const category1Count = document.getElementById('category1Count');
        const category2Count = document.getElementById('category2Count');
        const category3Count = document.getElementById('category3Count');
        const category1Chances = document.getElementById('category1Chances');
        const category2Chances = document.getElementById('category2Chances');
        const category3Chances = document.getElementById('category3Chances');
        const drawCategory1Btn = document.getElementById('drawCategory1Btn');
        const drawCategory2Btn = document.getElementById('drawCategory2Btn');
        const drawCategory3Btn = document.getElementById('drawCategory3Btn');
        const drawCategory1Count = document.getElementById('drawCategory1Count');
        const drawCategory2Count = document.getElementById('drawCategory2Count');
        const drawCategory3Count = document.getElementById('drawCategory3Count');
        const lotteryResult = document.getElementById('lotteryResult');
        const resultCategory = document.getElementById('resultCategory');
        const winnerName = document.getElementById('winnerName');
        const remainingChances = document.getElementById('remainingChances');
        const drawAgainBtn = document.getElementById('drawAgainBtn');
        const initialStatus = document.getElementById('initialStatus');
        const allParticipantsList = document.getElementById('allParticipantsList');
        const totalParticipantsCount = document.getElementById('totalParticipantsCount');

        // 检查localStorage是否可用
        function checkStorageAvailability() {
            try {
                const testKey = '__storage_test__';
                localStorage.setItem(testKey, testKey);
                localStorage.removeItem(testKey);
                return true;
            } catch (e) {
                console.warn('LocalStorage不可用，将使用内存存储:', e);
                return false;
            }
        }

        // 从存储加载数据
        function loadData() {
            if (useLocalStorage) {
                try {
                    const storedParticipants = localStorage.getItem(STORAGE_KEYS.PARTICIPANTS);
                    const storedMySubmissions = localStorage.getItem(STORAGE_KEYS.MY_SUBMISSIONS);
                    const storedMyChances = localStorage.getItem(STORAGE_KEYS.MY_CHANCES);
                    const storedContributorName = localStorage.getItem(STORAGE_KEYS.CONTRIBUTOR_NAME);

                    if (storedParticipants) participants = JSON.parse(storedParticipants);
                    if (storedMySubmissions) mySubmissions = JSON.parse(storedMySubmissions);
                    if (storedMyChances) myChances = JSON.parse(storedMyChances);
                    if (storedContributorName) contributorName = storedContributorName;
                    
                    // 设置称呼输入框的值
                    if (contributorName) {
                        contributorNameInput.value = contributorName;
                    }
                } catch (error) {
                    console.error('从LocalStorage加载数据失败，将使用内存存储:', error);
                    useLocalStorage = false;
                    showToast('本地存储不可用，您的数据将在页面刷新后丢失', 'warning');
                }
            }
        }

        // 保存数据到存储
        function saveData() {
            if (useLocalStorage) {
                try {
                    localStorage.setItem(STORAGE_KEYS.PARTICIPANTS, JSON.stringify(participants));
                    localStorage.setItem(STORAGE_KEYS.MY_SUBMISSIONS, JSON.stringify(mySubmissions));
                    localStorage.setItem(STORAGE_KEYS.MY_CHANCES, JSON.stringify(myChances));
                    localStorage.setItem(STORAGE_KEYS.CONTRIBUTOR_NAME, contributorName);
                } catch (error) {
                    console.error('保存数据到LocalStorage失败:', error);
                    useLocalStorage = false;
                    showToast('本地存储不可用，您的数据将在页面刷新后丢失', 'warning');
                }
            }
        }

        // 更新界面
        function updateUI() {
            // 更新我的提交列表（按分类）
            updateCategoryLists();
            
            // 更新全部参与人列表
            updateAllParticipantsList();
            
            // 更新抽奖按钮状态
            updateDrawButtons();
            
            // 检查并更新初始状态显示
            checkInitialStatus();
        }

        // 更新分类列表
        function updateCategoryLists() {
            // 假号列表
            const category1Names = mySubmissions[1];
            category1Count.textContent = `${category1Names.length}个`;
            category1Chances.textContent = myChances[1];
            drawCategory1Count.textContent = `${myChances[1]}次机会`;
            
            if (category1Names.length === 0) {
                category1Items.innerHTML = '<div class="text-gray-500 text-center py-2">暂无提交的名字</div>';
            } else {
                category1Items.innerHTML = '';
                category1Names.forEach(name => {
                    const item = document.createElement('div');
                    item.className = 'py-2 flex justify-between items-center';
                    item.innerHTML = `
                        <div class="flex items-center">
                            <span class="text-gray-800">${name}</span>
                            <span class="ml-2 text-xs bg-category1/10 text-category1 px-2 py-0.5 rounded">假号</span>
                        </div>
                        <button class="text-red-500 hover:text-red-700 transition-colors delete-btn" data-name="${name}" data-category="1">
                            <i class="fa fa-trash"></i>
                        </button>
                    `;
                    category1Items.appendChild(item);
                });
            }
            
            // 未实名真号列表
            const category2Names = mySubmissions[2];
            category2Count.textContent = `${category2Names.length}个`;
            category2Chances.textContent = myChances[2];
            drawCategory2Count.textContent = `${myChances[2]}次机会`;
            
            if (category2Names.length === 0) {
                category2Items.innerHTML = '<div class="text-gray-500 text-center py-2">暂无提交的名字</div>';
            } else {
                category2Items.innerHTML = '';
                category2Names.forEach(name => {
                    const item = document.createElement('div');
                    item.className = 'py-2 flex justify-between items-center';
                    item.innerHTML = `
                        <div class="flex items-center">
                            <span class="text-gray-800">${name}</span>
                            <span class="ml-2 text-xs bg-category2/10 text-category2 px-2 py-0.5 rounded">未实名真号</span>
                        </div>
                        <button class="text-red-500 hover:text-red-700 transition-colors delete-btn" data-name="${name}" data-category="2">
                            <i class="fa fa-trash"></i>
                        </button>
                    `;
                    category2Items.appendChild(item);
                });
            }
            
            // 实名真号列表
            const category3Names = mySubmissions[3];
            category3Count.textContent = `${category3Names.length}个`;
            category3Chances.textContent = myChances[3];
            drawCategory3Count.textContent = `${myChances[3]}次机会`;
            
            if (category3Names.length === 0) {
                category3Items.innerHTML = '<div class="text-gray-500 text-center py-2">暂无提交的名字</div>';
            } else {
                category3Items.innerHTML = '';
                category3Names.forEach(name => {
                    const item = document.createElement('div');
                    item.className = 'py-2 flex justify-between items-center';
                    item.innerHTML = `
                        <div class="flex items-center">
                            <span class="text-gray-800">${name}</span>
                            <span class="ml-2 text-xs bg-category3/10 text-category3 px-2 py-0.5 rounded">实名真号</span>
                        </div>
                        <button class="text-red-500 hover:text-red-700 transition-colors delete-btn" data-name="${name}" data-category="3">
                            <i class="fa fa-trash"></i>
                        </button>
                    `;
                    category3Items.appendChild(item);
                });
            }
            
            // 添加删除按钮事件
            document.querySelectorAll('.delete-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const name = this.getAttribute('data-name');
                    const category = parseInt(this.getAttribute('data-category'));
                    deleteName(name, category);
                });
            });
        }

        // 更新全部参与人列表
        function updateAllParticipantsList() {
            totalParticipantsCount.textContent = `${participants.length}个`;
            
            if (participants.length === 0) {
                allParticipantsList.innerHTML = '<div class="col-span-full text-gray-500 text-center py-4">暂无参与人</div>';
                return;
            }
            
            allParticipantsList.innerHTML = '';
            participants.forEach(p => {
                const item = document.createElement('div');
                item.className = 'bg-gray-50 rounded-lg p-3 flex items-center card-hover';
                
                // 检查是否是自己提交的名字
                const isMySubmission = mySubmissions[p.category].includes(p.name);
                const badge = isMySubmission ? 
                    '<span class="ml-2 text-xs bg-blue-100 text-blue-800 px-2 py-0.5 rounded">我提交的</span>' : '';
                
                // 根据分类设置颜色
                let categoryColor, categoryName;
                switch(p.category) {
                    case 1:
                        categoryColor = 'category1';
                        categoryName = '假号';
                        break;
                    case 2:
                        categoryColor = 'category2';
                        categoryName = '未实名真号';
                        break;
                    case 3:
                        categoryColor = 'category3';
                        categoryName = '实名真号';
                        break;
                }
                
                item.innerHTML = `
                    <div class="w-8 h-8 rounded-full bg-${categoryColor}/20 flex items-center justify-center mr-3">
                        <span class="text-${categoryColor} font-bold">${p.name.charAt(0)}</span>
                    </div>
                    <div class="flex-1">
                        <div class="font-medium text-gray-800">${p.name}</div>
                        <div class="text-xs text-gray-500">由 ${p.contributor}</div>
                    </div>
                    <span class="ml-2 text-xs bg-${categoryColor}/10 text-${categoryColor} px-2 py-0.5 rounded">${categoryName}</span>
                    ${badge}
                `;
                allParticipantsList.appendChild(item);
            });
        }

        // 更新抽奖按钮状态
        function updateDrawButtons() {
            // 假号抽奖按钮
            if (myChances[1] > 0) {
                drawCategory1Btn.disabled = false;
                drawCategory1Btn.classList.remove('opacity-50', 'cursor-not-allowed');
            } else {
                drawCategory1Btn.disabled = true;
                drawCategory1Btn.classList.add('opacity-50', 'cursor-not-allowed');
            }
            
            // 未实名真号抽奖按钮
            if (myChances[2] > 0) {
                drawCategory2Btn.disabled = false;
                drawCategory2Btn.classList.remove('opacity-50', 'cursor-not-allowed');
            } else {
                drawCategory2Btn.disabled = true;
                drawCategory2Btn.classList.add('opacity-50', 'cursor-not-allowed');
            }
            
            // 实名真号抽奖按钮
            if (myChances[3] > 0) {
                drawCategory3Btn.disabled = false;
                drawCategory3Btn.classList.remove('opacity-50', 'cursor-not-allowed');
            } else {
                drawCategory3Btn.disabled = true;
                drawCategory3Btn.classList.add('opacity-50', 'cursor-not-allowed');
            }
        }

        // 检查并更新初始状态显示
        function checkInitialStatus() {
            const totalChances = myChances[1] + myChances[2] + myChances[3];
            if (totalChances > 0) {
                initialStatus.classList.add('hidden');
            } else {
                initialStatus.classList.remove('hidden');
            }
        }

        // 添加名字
        function addName() {
            const name = personNameInput.value.trim();
            contributorName = contributorNameInput.value.trim();
            
            if (!name || !contributorName) {
                showToast('请填写您的称呼和参与人姓名', 'error');
                return;
            }
            
            // 获取选中的分类
            const category = parseInt(document.querySelector('input[name="category"]:checked').value);
            
            // 检查名字是否已存在（所有分类）
            const exists = participants.some(p => p.name === name);
            if (exists) {
                showToast('该名字已存在', 'error');
                return;
            }
            
            // 添加到参与者列表
            participants.push({
                name: name,
                contributor: contributorName,
                category: category,
                timestamp: new Date().getTime()
            });
            
            // 添加到我的提交
            mySubmissions[category].push(name);
            
            // 增加对应分类的抽奖机会
            myChances[category]++;
            
            // 保存数据
            saveData();
            
            // 清空输入
            personNameInput.value = '';
            personNameInput.focus();
            
            // 更新界面
            updateUI();
            
            // 显示成功提示
            let categoryName;
            switch(category) {
                case 1: categoryName = '假号'; break;
                case 2: categoryName = '未实名真号'; break;
                case 3: categoryName = '实名真号'; break;
            }
            showToast(`已添加 "${name}" 到${categoryName}名单`, 'success');
        }

        // 删除名字
        function deleteName(name, category) {
            if (!confirm(`确定要删除 "${name}" 吗？`)) return;
            
            // 从参与者列表中删除
            participants = participants.filter(p => !(p.name === name && p.category === category));
            
            // 从我的提交中删除
            mySubmissions[category] = mySubmissions[category].filter(n => n !== name);
            
            // 减少对应分类的抽奖机会（如果有剩余）
            if (myChances[category] > 0) {
                myChances[category]--;
            }
            
            // 保存数据
            saveData();
            
            // 更新界面
            updateUI();
            
            // 显示提示
            showToast(`已删除 "${name}"`, 'info');
        }

        // 抽奖
        function drawLottery(category) {
            if (myChances[category] <= 0) return;
            
            // 过滤掉自己提交的名字，并且只选择当前分类的参与者
            const availableParticipants = participants.filter(p => 
                p.category === category && !mySubmissions[category].includes(p.name)
            );
            
            if (availableParticipants.length === 0) {
                let categoryName;
                switch(category) {
                    case 1: categoryName = '假号'; break;
                    case 2: categoryName = '未实名真号'; break;
                    case 3: categoryName = '实名真号'; break;
                }
                showToast(`没有可抽取的${categoryName}名单，请让其他人也提交${categoryName}名单`, 'warning');
                return;
            }
            
            // 随机选择一个名字
            const winner = availableParticipants[Math.floor(Math.random() * availableParticipants.length)];
            
            // 减少对应分类的抽奖机会
            myChances[category]--;
            
            // 保存数据
            saveData();
            
            // 更新当前分类
            currentCategory = category;
            
            // 显示结果
            let categoryName, categoryColor;
            switch(category) {
                case 1:
                    categoryName = '假号';
                    categoryColor = 'bg-category1 text-white';
                    break;
                case 2:
                    categoryName = '未实名真号';
                    categoryColor = 'bg-category2 text-white';
                    break;
                case 3:
                    categoryName = '实名真号';
                    categoryColor = 'bg-category3 text-white';
                    break;
            }
            
            resultCategory.className = `px-3 py-1 rounded-full text-sm ${categoryColor}`;
            resultCategory.textContent = categoryName;
            winnerName.textContent = winner.name;
            remainingChances.textContent = myChances[category];
            lotteryResult.classList.remove('hidden');
            
            // 更新抽奖按钮状态
            updateDrawButtons();
            
            // 检查并更新初始状态显示
            checkInitialStatus();
            
            // 添加动画效果
            animateWinner();
        }

        // 中奖动画
        function animateWinner() {
            const winnerElement = document.getElementById('winnerName');
            winnerElement.classList.add('animate-pulse');
            
            setTimeout(() => {
                winnerElement.classList.remove('animate-pulse');
            }, 2000);
        }

        // 显示提示框
        function showToast(message, type = 'info') {
            // 创建提示框
            const toast = document.createElement('div');
            
            // 设置样式和类
            let bgColor, textColor, icon;
            switch(type) {
                case 'success':
                    bgColor = 'bg-green-50';
                    textColor = 'text-green-800';
                    icon = 'fa-check-circle';
                    break;
                case 'error':
                    bgColor = 'bg-red-50';
                    textColor = 'text-red-800';
                    icon = 'fa-exclamation-circle';
                    break;
                case 'warning':
                    bgColor = 'bg-yellow-50';
                    textColor = 'text-yellow-800';
                    icon = 'fa-exclamation-triangle';
                    break;
                default:
                    bgColor = 'bg-blue-50';
                    textColor = 'text-blue-800';
                    icon = 'fa-info-circle';
            }
            
            toast.className = `${bgColor} ${textColor} border-l-4 border-green-400 p-4 fixed top-4 right-4 rounded shadow-lg z-50 transform transition-all duration-500 ease-in-out translate-x-full`;
            toast.innerHTML = `
                <div class="flex items-center">
                    <div class="flex-shrink-0">
                        # -
