<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bella Cucina - Menu Builder</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      box-sizing: border-box;
    }
    
    * {
      box-sizing: border-box;
    }
    
    html, body {
      height: 100%;
      margin: 0;
      padding: 0;
    }
    
    .category-card {
      transition: all 0.3s ease;
    }
    
    .category-card:hover {
      transform: translateY(-4px);
    }
    
    .menu-item {
      transition: all 0.2s ease;
    }
    
    .menu-item:hover {
      transform: scale(1.02);
    }
    
    .item-selected {
      border-width: 3px;
    }
    
    .fade-in {
      animation: fadeIn 0.3s ease-in;
    }
    
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
    
    .order-summary {
      position: sticky;
      top: 20px;
    }

    .modal-backdrop {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: rgba(0, 0, 0, 0.6);
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 16px;
      z-index: 1000;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
</head>
<body class="w-full h-full">
  <div id="app" class="w-full h-full"></div>

  <script>
    const defaultConfig = {
      restaurant_name: "Bella Cucina",
      tagline: "สร้างออเดอร์ของคุณเอง",
      category_1: "อาหารเรียกน้ำย่อย",
      category_2: "พาสต้า",
      category_3: "ของหวาน",
      background_color: "#f8f9fa",
      surface_color: "#ffffff",
      text_color: "#2c3e50",
      primary_action_color: "#e74c3c",
      secondary_action_color: "#95a5a6",
      font_family: "Inter",
      font_size: 16
    };

    let config = {};
    let selectedCategory = null;
    let orderItems = [];
    let showPaymentGame = false;
    let selectedPaymentAmount = null;
    let selectedChangeAmount = null;
    let stars = 0;
    let gameResult = null;

    const menuData = {
      "อาหารเรียกน้ำย่อย": [
        { id: 1, name: "Bruschetta", price: 8, emoji: "🍞", description: "ขนมปังกรอบโรยมะเขือเทศสด" },
        { id: 2, name: "Calamari Fritti", price: 12, emoji: "🦑", description: "ปลาหมึกทอดกรอบ" },
        { id: 3, name: "Caprese Salad", price: 10, emoji: "🧀", description: "มอสซาเรลล่าสดใส่มะเขือเทศ" },
        { id: 4, name: "Antipasto Platter", price: 12, emoji: "🍖", description: "จานรวมเนื้อรมควันและชีส" }
      ],
      "พาสต้า": [
        { id: 5, name: "Spaghetti", basePrice: 14, emoji: "🍝", description: "สปาเก็ตตี้คลาสสิก", toppings: [
          { name: "ไก่ย่าง", price: 4 },
          { name: "กุ้ง", price: 6 },
          { name: "มีทบอล", price: 4 },
          { name: "ผักเพิ่ม", price: 2 }
        ]},
        { id: 6, name: "Penne Arrabbiata", basePrice: 15, emoji: "🌶️", description: "เพนเน่ซอสมะเขือเทศเผ็ด", toppings: [
          { name: "ไก่ย่าง", price: 4 },
          { name: "กุ้ง", price: 6 },
          { name: "มีทบอล", price: 4 },
          { name: "ผักเพิ่ม", price: 2 }
        ]},
        { id: 7, name: "Fettuccine Alfredo", basePrice: 16, emoji: "🧈", description: "เฟตทูชินี่ครีมเข้มข้น", toppings: [
          { name: "ไก่ย่าง", price: 4 },
          { name: "กุ้ง", price: 6 },
          { name: "มีทบอล", price: 4 },
          { name: "ผักเพิ่ม", price: 2 }
        ]},
        { id: 8, name: "Carbonara", basePrice: 18, emoji: "🥓", description: "พาสต้าเบคอนครีมไข่", toppings: [
          { name: "ไก่ย่าง", price: 4 },
          { name: "กุ้ง", price: 6 },
          { name: "มีทบอล", price: 4 },
          { name: "ผักเพิ่ม", price: 2 }
        ]}
      ],
      "ของหวาน": [
        { id: 9, name: "Tiramisu", price: 9, emoji: "☕", description: "เค้กกาแฟอิตาลีคลาสสิก" },
        { id: 10, name: "Panna Cotta", price: 8, emoji: "🍮", description: "ครีมหวานเนียนนุ่ม" },
        { id: 11, name: "Cannoli", price: 7, emoji: "🥐", description: "ขนมอบกรอบไส้ครีม" },
        { id: 12, name: "Gelato", price: 6, emoji: "🍨", description: "ไอศกรีมอิตาเลียน 2 สกู๊ป" }
      ]
    };

    function getTotal() {
      const subtotal = orderItems.reduce((sum, item) => sum + item.totalPrice, 0);
      const tax = subtotal * 0.08;
      const total = subtotal + tax;
      return { subtotal, tax, total };
    }

    function generateChangeOptions(payment, total) {
      const correctChange = payment - total;
      const options = [correctChange];
      
      const diff1 = Math.floor(Math.random() * 15) + 5;
      const diff2 = Math.floor(Math.random() * 15) + 5;
      
      options.push(correctChange + diff1);
      options.push(correctChange - diff2);
      
      return options.sort(() => Math.random() - 0.5);
    }

    function renderApp() {
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const baseFontStack = 'system-ui, -apple-system, sans-serif';
      
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      
      const categoryKey1 = config.category_1 || defaultConfig.category_1;
      const categoryKey2 = config.category_2 || defaultConfig.category_2;
      const categoryKey3 = config.category_3 || defaultConfig.category_3;
      
      const { subtotal, tax, total } = getTotal();
      
      app.style.fontFamily = `${customFont}, ${baseFontStack}`;
      app.style.backgroundColor = bgColor;
      app.style.color = textColor;
      
      app.innerHTML = `
        <div class="w-full h-full" style="overflow-y: auto;">
          <div class="w-full min-h-full px-4 py-8 md:px-8">
            <!-- Header -->
            <header class="text-center mb-12">
              <h1 class="font-bold mb-2" style="font-size: ${baseSize * 2.5}px; color: ${textColor}">
                ${config.restaurant_name || defaultConfig.restaurant_name}
              </h1>
              <p class="mb-1" style="font-size: ${baseSize * 1.2}px; color: ${secondaryColor}">
                ${config.tagline || defaultConfig.tagline}
              </p>
              <div class="inline-block px-4 py-1 rounded-full mt-2" style="background-color: ${primaryColor}20; color: ${primaryColor}; font-size: ${baseSize * 0.9}px;">
                ออนไลน์ • พร้อมรับออเดอร์
              </div>
            </header>

            <div class="max-w-7xl mx-auto">
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <!-- Menu Section -->
                <div class="lg:col-span-2">
                  <!-- Category Selection -->
                  <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-8">
                    <button onclick="selectCategory('${categoryKey1}')" 
                            class="category-card p-6 rounded-2xl shadow-md text-center cursor-pointer"
                            style="background-color: ${surfaceColor}; border: 2px solid ${selectedCategory === categoryKey1 ? primaryColor : 'transparent'}">
                      <div style="font-size: ${baseSize * 3}px;" class="mb-2">🍴</div>
                      <h3 class="font-semibold" style="font-size: ${baseSize * 1.1}px; color: ${textColor}">${categoryKey1}</h3>
                      <p style="font-size: ${baseSize * 0.85}px; color: ${secondaryColor}">฿8-12</p>
                    </button>
                    
                    <button onclick="selectCategory('${categoryKey2}')" 
                            class="category-card p-6 rounded-2xl shadow-md text-center cursor-pointer"
                            style="background-color: ${surfaceColor}; border: 2px solid ${selectedCategory === categoryKey2 ? primaryColor : 'transparent'}">
                      <div style="font-size: ${baseSize * 3}px;" class="mb-2">🍝</div>
                      <h3 class="font-semibold" style="font-size: ${baseSize * 1.1}px; color: ${textColor}">${categoryKey2}</h3>
                      <p style="font-size: ${baseSize * 0.85}px; color: ${secondaryColor}">฿14-18</p>
                    </button>
                    
                    <button onclick="selectCategory('${categoryKey3}')" 
                            class="category-card p-6 rounded-2xl shadow-md text-center cursor-pointer"
                            style="background-color: ${surfaceColor}; border: 2px solid ${selectedCategory === categoryKey3 ? primaryColor : 'transparent'}">
                      <div style="font-size: ${baseSize * 3}px;" class="mb-2">🍰</div>
                      <h3 class="font-semibold" style="font-size: ${baseSize * 1.1}px; color: ${textColor}">${categoryKey3}</h3>
                      <p style="font-size: ${baseSize * 0.85}px; color: ${secondaryColor}">฿6-9</p>
                    </button>
                  </div>

                  <!-- Menu Items -->
                  <div id="menuItems"></div>
                </div>

                <!-- Order Summary -->
                <div class="lg:col-span-1">
                  <div class="order-summary rounded-2xl shadow-lg p-6" style="background-color: ${surfaceColor}">
                    <h2 class="font-bold mb-4" style="font-size: ${baseSize * 1.5}px; color: ${textColor}">ออเดอร์ของคุณ</h2>
                    <div id="orderList" class="space-y-3 mb-6 min-h-32"></div>
                    <div class="border-t pt-4" style="border-color: ${secondaryColor}40">
                      <div class="flex justify-between items-center mb-2">
                        <span style="font-size: ${baseSize}px; color: ${secondaryColor}">รวมย่อย</span>
                        <span class="font-semibold" style="font-size: ${baseSize}px; color: ${textColor}">฿${subtotal.toFixed(2)}</span>
                      </div>
                      <div class="flex justify-between items-center mb-2">
                        <span style="font-size: ${baseSize}px; color: ${secondaryColor}">ภาษี (8%)</span>
                        <span class="font-semibold" style="font-size: ${baseSize}px; color: ${textColor}">฿${tax.toFixed(2)}</span>
                      </div>
                      <div class="flex justify-between items-center pt-2 border-t" style="border-color: ${secondaryColor}40">
                        <span class="font-bold" style="font-size: ${baseSize * 1.2}px; color: ${textColor}">รวมทั้งหมด</span>
                        <span class="font-bold" style="font-size: ${baseSize * 1.2}px; color: ${primaryColor}">฿${total.toFixed(2)}</span>
                      </div>
                      ${orderItems.length > 0 ? `
                        <button onclick="startPaymentGame()" 
                                class="w-full mt-4 py-3 rounded-xl font-semibold transition-all"
                                style="background-color: ${primaryColor}; color: white; font-size: ${baseSize}px;">
                          💰 ฝึกคำนวณเงินทอน
                        </button>
                        ${stars > 0 ? `
                          <div class="text-center mt-3 py-2 rounded-lg" style="background-color: #ffd70020; font-size: ${baseSize}px; color: ${textColor}">
                            ⭐ คุณได้ ${stars} ดาวแล้ว!
                          </div>
                        ` : ''}
                      ` : ''}
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      `;

      renderMenuItems();
      renderOrderList();
      
      if (showPaymentGame) {
        renderPaymentModal();
      }
    }

    function renderMenuItems() {
      const menuItems = document.getElementById('menuItems');
      if (!menuItems) return;
      
      const baseSize = config.font_size || defaultConfig.font_size;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      
      if (!selectedCategory) {
        menuItems.innerHTML = `
          <div class="text-center py-12" style="color: ${secondaryColor}">
            <div style="font-size: ${baseSize * 4}px;" class="mb-4">👆</div>
            <p style="font-size: ${baseSize * 1.1}px;">เลือกหมวดหมู่เพื่อดูเมนู</p>
          </div>
        `;
        return;
      }
      
      const items = menuData[selectedCategory] || [];
      
      menuItems.innerHTML = items.map(item => {
        const isInOrder = orderItems.some(oi => oi.id === item.id);
        return `
          <div class="menu-item rounded-xl shadow-md p-5 cursor-pointer mb-4 ${isInOrder ? 'item-selected' : ''}" 
               style="background-color: ${surfaceColor}; border: 2px solid ${isInOrder ? primaryColor : 'transparent'}"
               onclick="toggleItem(${item.id})">
            <div class="flex items-start gap-4">
              <div class="text-center flex-shrink-0">
                <div style="font-size: ${baseSize * 3.5}px;" class="mb-1">${item.emoji}</div>
              </div>
              <div class="flex-grow">
                <div class="flex justify-between items-start mb-2">
                  <h3 class="font-semibold" style="font-size: ${baseSize * 1.2}px; color: ${textColor}">${item.name}</h3>
                  <span class="font-bold" style="font-size: ${baseSize * 1.1}px; color: ${primaryColor}">
                    ฿${item.price || item.basePrice}${item.basePrice ? '+' : ''}
                  </span>
                </div>
                <p style="font-size: ${baseSize * 0.9}px; color: ${secondaryColor}" class="mb-3">
                  ${item.description}
                </p>
                ${item.toppings ? `
                  <div id="toppings-${item.id}" class="hidden mt-3 space-y-2 pt-3 border-t" style="border-color: ${secondaryColor}40">
                    <p class="font-semibold" style="font-size: ${baseSize * 0.9}px; color: ${textColor}">เลือกท็อปปิ้ง:</p>
                    ${item.toppings.map((topping, idx) => `
                      <label class="flex items-center gap-2 cursor-pointer" onclick="event.stopPropagation()">
                        <input type="checkbox" 
                               id="topping-${item.id}-${idx}"
                               class="w-4 h-4 cursor-pointer" 
                               style="accent-color: ${primaryColor}">
                        <span style="font-size: ${baseSize * 0.9}px; color: ${textColor}">${topping.name} (+฿${topping.price})</span>
                      </label>
                    `).join('')}
                  </div>
                ` : ''}
              </div>
            </div>
          </div>
        `;
      }).join('');
    }

    function renderOrderList() {
      const orderList = document.getElementById('orderList');
      if (!orderList) return;
      
      const baseSize = config.font_size || defaultConfig.font_size;
      const textColor = config.text_color || defaultConfig.text_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      
      if (orderItems.length === 0) {
        orderList.innerHTML = `
          <div class="text-center py-4" style="color: ${secondaryColor}">
            <p style="font-size: ${baseSize * 0.9}px;">ออเดอร์ของคุณว่างเปล่า</p>
          </div>
        `;
      } else {
        orderList.innerHTML = orderItems.map(item => {
          const toppingsText = item.selectedToppings && item.selectedToppings.length > 0
            ? `<div style="font-size: ${baseSize * 0.75}px; color: ${secondaryColor}" class="mt-1">
                 ${item.selectedToppings.map(t => `• ${t.name} +฿${t.price}`).join('<br>')}
               </div>`
            : '';
          
          return `
            <div class="flex justify-between items-start gap-2 pb-3 border-b" style="border-color: ${secondaryColor}20">
              <div class="flex-grow">
                <div style="font-size: ${baseSize * 0.95}px; color: ${textColor}" class="font-medium">${item.name}</div>
                ${toppingsText}
              </div>
              <div class="text-right flex-shrink-0">
                <div style="font-size: ${baseSize * 0.95}px; color: ${textColor}" class="font-semibold">฿${item.totalPrice.toFixed(2)}</div>
                <button onclick="removeItem(${item.id})" 
                        class="mt-1 px-2 py-1 rounded" 
                        style="font-size: ${baseSize * 0.75}px; background-color: ${secondaryColor}20; color: ${secondaryColor}">
                  ลบ
                </button>
              </div>
            </div>
          `;
        }).join('');
      }
    }

    function renderPaymentModal() {
      const baseSize = config.font_size || defaultConfig.font_size;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      
      const { total } = getTotal();
      
      const modalDiv = document.createElement('div');
      modalDiv.className = 'modal-backdrop';
      modalDiv.id = 'paymentModal';
      
      let changeOptions = [];
      if (selectedPaymentAmount) {
        changeOptions = generateChangeOptions(selectedPaymentAmount, total);
      }
      
      modalDiv.innerHTML = `
        <div class="rounded-2xl shadow-2xl p-8 max-w-md w-full fade-in" style="background-color: ${surfaceColor}; max-height: 90%; overflow-y: auto;">
          <div class="text-center mb-6">
            <h2 class="font-bold mb-2" style="font-size: ${baseSize * 1.8}px; color: ${textColor}">
              🎮 เกมคำนวณเงินทอน
            </h2>
            <p style="font-size: ${baseSize}px; color: ${secondaryColor}">
              เลือกธนบัตรที่จ่าย และคำนวณเงินทอน
            </p>
          </div>
          
          <div class="mb-6 p-4 rounded-xl text-center" style="background-color: ${primaryColor}20">
            <p style="font-size: ${baseSize * 0.9}px; color: ${secondaryColor}">
              ยอดที่ต้องจ่าย
            </p>
            <p class="font-bold" style="font-size: ${baseSize * 2}px; color: ${primaryColor}">
              ฿${total.toFixed(2)}
            </p>
          </div>
          
          <div class="mb-6">
            <p class="font-semibold mb-3" style="font-size: ${baseSize}px; color: ${textColor}">1️⃣ เลือกธนบัตรที่จ่าย:</p>
            <div class="grid grid-cols-3 gap-3">
              ${[20, 50, 100, 500, 1000].map(amount => `
                <button onclick="selectPayment(${amount})" 
                        class="py-3 px-2 rounded-lg font-bold transition-all"
                        style="background-color: ${selectedPaymentAmount === amount ? primaryColor : secondaryColor + '40'}; 
                               color: ${selectedPaymentAmount === amount ? 'white' : textColor}; 
                               border: 2px solid ${selectedPaymentAmount === amount ? primaryColor : 'transparent'};
                               font-size: ${baseSize * 0.95}px;">
                  ฿${amount}
                </button>
              `).join('')}
            </div>
          </div>
          
          ${selectedPaymentAmount ? `
            <div class="mb-6">
              <p class="font-semibold mb-3" style="font-size: ${baseSize}px; color: ${textColor}">2️⃣ เงินทอนที่ถูกต้องคือ:</p>
              <div class="grid grid-cols-3 gap-3">
                ${changeOptions.map(amount => `
                  <button onclick="selectChange(${amount})" 
                          class="py-3 px-2 rounded-lg font-bold transition-all"
                          style="background-color: ${selectedChangeAmount === amount ? primaryColor : secondaryColor + '40'}; 
                                 color: ${selectedChangeAmount === amount ? 'white' : textColor}; 
                                 border: 2px solid ${selectedChangeAmount === amount ? primaryColor : 'transparent'};
                                 font-size: ${baseSize * 0.95}px;">
                    ฿${amount.toFixed(2)}
                  </button>
                `).join('')}
              </div>
            </div>
          ` : ''}
          
          ${selectedPaymentAmount && selectedChangeAmount !== null && !gameResult ? `
            <button onclick="checkAnswer()" 
                    class="w-full py-3 rounded-xl font-semibold mb-3"
                    style="background-color: ${primaryColor}; color: white; font-size: ${baseSize}px;">
              ✓ ตรวจคำตอบ
            </button>
          ` : ''}
          
          ${gameResult ? `
            <div class="text-center p-6 rounded-xl mb-4 fade-in" 
                 style="background-color: ${gameResult.correct ? '#4caf5020' : '#f4433620'}; border: 2px solid ${gameResult.correct ? '#4caf50' : '#f44336'};">
              <div style="font-size: ${baseSize * 3}px;">${gameResult.correct ? '⭐' : '❌'}</div>
              <p class="font-bold mt-2" style="font-size: ${baseSize * 1.2}px; color: ${gameResult.correct ? '#4caf50' : '#f44336'};">
                ${gameResult.correct ? 'ถูกต้อง! คุณได้ 1 ดาว' : 'ผิดนะ! ลองใหม่อีกครั้ง'}
              </p>
              <p style="font-size: ${baseSize}px; color: ${secondaryColor}; margin-top: 8px;">
                ${gameResult.message}
              </p>
            </div>
          ` : ''}
          
          <button onclick="closePaymentGame()" 
                  class="w-full py-3 rounded-xl font-semibold"
                  style="background-color: ${secondaryColor}40; color: ${textColor}; font-size: ${baseSize * 0.9}px;">
            ${gameResult && gameResult.correct ? 'เริ่มเกมใหม่' : 'ปิด'}
          </button>
        </div>
      `;
      
      document.body.appendChild(modalDiv);
    }

    window.selectCategory = function(category) {
      selectedCategory = category;
      renderApp();
    };

    window.toggleItem = function(itemId) {
      const existingItemIndex = orderItems.findIndex(oi => oi.id === itemId);
      
      if (existingItemIndex >= 0) {
        orderItems.splice(existingItemIndex, 1);
        renderApp();
        return;
      }
      
      let item;
      for (const category in menuData) {
        item = menuData[category].find(i => i.id === itemId);
        if (item) break;
      }
      
      if (!item) return;
      
      if (item.toppings) {
        const toppingsDiv = document.getElementById(`toppings-${itemId}`);
        if (toppingsDiv && toppingsDiv.classList.contains('hidden')) {
          toppingsDiv.classList.remove('hidden');
        } else if (toppingsDiv) {
          const selectedToppings = [];
          item.toppings.forEach((topping, idx) => {
            const checkbox = document.getElementById(`topping-${itemId}-${idx}`);
            if (checkbox && checkbox.checked) {
              selectedToppings.push(topping);
            }
          });
          
          const totalPrice = item.basePrice + selectedToppings.reduce((sum, t) => sum + t.price, 0);
          
          orderItems.push({
            id: item.id,
            name: item.name,
            basePrice: item.basePrice,
            selectedToppings: selectedToppings,
            totalPrice: totalPrice
          });
          
          renderApp();
        }
      } else {
        orderItems.push({
          id: item.id,
          name: item.name,
          totalPrice: item.price
        });
        
        renderApp();
      }
    };

    window.removeItem = function(itemId) {
      const index = orderItems.findIndex(oi => oi.id === itemId);
      if (index >= 0) {
        orderItems.splice(index, 1);
        renderApp();
      }
    };

    window.startPaymentGame = function() {
      showPaymentGame = true;
      selectedPaymentAmount = null;
      selectedChangeAmount = null;
      gameResult = null;
      renderApp();
    };

    window.closePaymentGame = function() {
      showPaymentGame = false;
      selectedPaymentAmount = null;
      selectedChangeAmount = null;
      gameResult = null;
      
      const modal = document.getElementById('paymentModal');
      if (modal) {
        modal.remove();
      }
      
      renderApp();
    };

    window.selectPayment = function(amount) {
      selectedPaymentAmount = amount;
      selectedChangeAmount = null;
      gameResult = null;
      
      const modal = document.getElementById('paymentModal');
      if (modal) {
        modal.remove();
      }
      renderPaymentModal();
    };

    window.selectChange = function(amount) {
      selectedChangeAmount = amount;
      
      const modal = document.getElementById('paymentModal');
      if (modal) {
        modal.remove();
      }
      renderPaymentModal();
    };

    window.checkAnswer = function() {
      const { total } = getTotal();
      const correctChange = parseFloat((selectedPaymentAmount - total).toFixed(2));
      const selectedChange = parseFloat(selectedChangeAmount.toFixed(2));
      
      if (Math.abs(selectedChange - correctChange) < 0.01) {
        stars++;
        gameResult = {
          correct: true,
          message: `เงินทอน ฿${correctChange.toFixed(2)} ถูกต้อง`
        };
      } else {
        gameResult = {
          correct: false,
          message: `คำตอบที่ถูกต้องคือ ฿${correctChange.toFixed(2)}`
        };
      }
      
      const modal = document.getElementById('paymentModal');
      if (modal) {
        modal.remove();
      }
      renderPaymentModal();
      
      if (gameResult.correct) {
        setTimeout(() => {
          closePaymentGame();
        }, 2500);
      }
    };

    // Initialize
    config = { ...defaultConfig };
    renderApp();
  </script>
</body>
</html>
