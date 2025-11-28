<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Taw Cucina – เกมร้านอาหาร & เกมคำนวณเงินทอน</title>

  <style>
    * {
      box-sizing: border-box;
    }

    html, body {
      height: 100%;
      margin: 0;
      padding: 0;
      font-family: system-ui, -apple-system, sans-serif;
      background-color: #f8f9fa;
      color: #2c3e50;
    }

    .w-full { width: 100%; }
    .h-full { height: 100%; }
    .text-center { text-align: center; }
    .text-right { text-align: right; }
    .font-bold { font-weight: 700; }
    .font-semibold { font-weight: 600; }
    .mt-2 { margin-top: 0.5rem; }
    .mt-3 { margin-top: 0.75rem; }
    .mt-4 { margin-top: 1rem; }
    .mb-1 { margin-bottom: 0.25rem; }
    .mb-2 { margin-bottom: 0.5rem; }
    .mb-3 { margin-bottom: 0.75rem; }
    .mb-4 { margin-bottom: 1rem; }
    .mb-6 { margin-bottom: 1.5rem; }
    .mb-8 { margin-bottom: 2rem; }
    .py-1 { padding-top: 0.25rem; padding-bottom: 0.25rem; }
    .py-2 { padding-top: 0.5rem; padding-bottom: 0.5rem; }
    .py-3 { padding-top: 0.75rem; padding-bottom: 0.75rem; }
    .py-4 { padding-top: 1rem; padding-bottom: 1rem; }
    .py-8 { padding-top: 2rem; padding-bottom: 2rem; }
    .px-2 { padding-left: 0.5rem; padding-right: 0.5rem; }
    .px-4 { padding-left: 1rem; padding-right: 1rem; }
    .p-4 { padding: 1rem; }
    .p-5 { padding: 1.25rem; }
    .p-6 { padding: 1.5rem; }
    .p-8 { padding: 2rem; }
    .shadow-lg { box-shadow: 0 8px 20px rgba(0,0,0,0.12); }
    .flex { display: flex; }
    .flex-col { flex-direction: column; }
    .flex-row { flex-direction: row; }
    .items-start { align-items: flex-start; }
    .items-center { align-items: center; }
    .justify-between { justify-content: space-between; }
    .gap-2 { gap: 0.5rem; }
    .gap-3 { gap: 0.75rem; }
    .gap-4 { gap: 1rem; }
    .border-top { border-top: 1px solid rgba(149,165,166,0.2); }
    .border-bottom { border-bottom: 1px solid rgba(149,165,166,0.2); }
    .rounded-full { border-radius: 999px; }
    .min-h-32 { min-height: 8rem; }
    .cursor-pointer { cursor: pointer; }
    .hidden { display: none; }

    .page-container {
      width: 100%;
      min-height: 100vh;
      padding: 2rem 1rem;
    }

    .max-width {
      max-width: 1120px;
      margin: 0 auto;
    }

    .grid {
      display: grid;
      gap: 2rem;
    }

    @media (min-width: 1024px) {
      .grid-3 {
        grid-template-columns: 2fr 1fr;
      }
      .grid-3-cols {
        grid-template-columns: repeat(3, 1fr);
      }
    }

    @media (max-width: 1023px) {
      .grid-3 {
        grid-template-columns: 1fr;
      }
      .grid-3-cols {
        grid-template-columns: 1fr;
      }
    }

    .category-card {
      transition: all 0.3s ease;
      border-radius: 16px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.08);
      border: 2px solid transparent;
      background-color: #ffffff;
    }

    .category-card:hover {
      transform: translateY(-4px);
    }

    .menu-item {
      transition: all 0.2s ease;
      border-radius: 16px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.08);
      background-color: #ffffff;
    }

    .menu-item:hover {
      transform: scale(1.02);
    }

    .item-selected {
      border-width: 3px !important;
    }

    .order-summary {
      position: sticky;
      top: 20px;
    }

    .btn {
      border: none;
      cursor: pointer;
      border-radius: 12px;
      padding: 0.75rem 1rem;
      font-weight: 600;
    }

    .btn-primary {
      background-color: #e74c3c;
      color: #fff;
    }

    .btn-secondary {
      background-color: rgba(149,165,166,0.2);
      color: #2c3e50;
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

    .fade-in {
      animation: fadeIn 0.3s ease-in;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body class="w-full h-full">
  <div id="app" class="w-full h-full"></div>

  <script>
    // ----------------- ข้อมูลเมนู -----------------
    const restaurantName = "Taw Cucina";
    const tagline = "สร้างออเดอร์ของคุณเอง";

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

    // ----------------- ตัวแปรสถานะ -----------------
    let selectedCategory = null;
    let orderItems = [];
    let showPaymentGame = false;
    let selectedPaymentAmount = null;
    let selectedChangeAmount = null;
    let stars = 0;
    let gameResult = null; // { correct: true/false, message: "" }

    // ----------------- ฟังก์ชันคำนวณ -----------------
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

    // ----------------- เรนเดอร์หน้าเว็บหลัก -----------------
    function renderApp() {
      const app = document.getElementById("app");
      const { subtotal, tax, total } = getTotal();

      app.innerHTML = `
        <div class="page-container">
          <header class="text-center mb-8">
            <h1 class="font-bold mb-2" style="font-size: 2.2rem;">${restaurantName}</h1>
            <p class="mb-1" style="font-size: 1.1rem; color: #7f8c8d;">${tagline}</p>
            <div class="inline-block px-4 py-1 rounded-full mt-2"
                 style="background-color: rgba(231,76,60,0.12); color: #e74c3c; font-size: 0.9rem;">
              ออนไลน์ • พร้อมรับออเดอร์
            </div>
          </header>

          <div class="max-width">
            <div class="grid grid-3">
              <!-- ส่วนเมนู -->
              <div>
                <div class="grid grid-3-cols mb-8">
                  ${renderCategoryButtons()}
                </div>
                <div id="menuItems"></div>
              </div>

              <!-- สรุปออเดอร์ -->
              <div>
                <div class="order-summary shadow-lg p-6" style="background-color: #ffffff; border-radius: 16px;">
                  <h2 class="font-bold mb-4" style="font-size: 1.4rem;">ออเดอร์ของคุณ</h2>
                  <div id="orderList" class="mb-6 min-h-32"></div>
                  <div class="border-top pt-4">
                    <div class="flex justify-between items-center mb-2">
                      <span style="font-size: 1rem; color: #7f8c8d;">รวมย่อย</span>
                      <span class="font-semibold" style="font-size: 1rem;">฿${subtotal.toFixed(2)}</span>
                    </div>
                    <div class="flex justify-between items-center mb-2">
                      <span style="font-size: 1rem; color: #7f8c8d;">ภาษี (8%)</span>
                      <span class="font-semibold" style="font-size: 1rem;">฿${tax.toFixed(2)}</span>
                    </div>
                    <div class="flex justify-between items-center pt-2 border-top">
                      <span class="font-bold" style="font-size: 1.1rem;">รวมทั้งหมด</span>
                      <span class="font-bold" style="font-size: 1.1rem; color: #e74c3c;">฿${total.toFixed(2)}</span>
                    </div>

                    ${orderItems.length > 0 ? `
                      <button class="btn btn-primary w-full mt-4" onclick="startPaymentGame()">
                        💰 ฝึกคำนวณเงินทอน
                      </button>
                      ${stars > 0 ? `
                        <div class="text-center mt-3 py-2"
                             style="background-color: rgba(255,215,0,0.2); border-radius: 8px;">
                          ⭐ คุณได้ ${stars} ดาวแล้ว!
                        </div>
                      ` : ``}
                    ` : ``}
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

    function renderCategoryButtons() {
      const categories = [
        { key: "อาหารเรียกน้ำย่อย", emoji: "🍴", range: "฿8-12" },
        { key: "พาสต้า",          emoji: "🍝", range: "฿14-18" },
        { key: "ของหวาน",        emoji: "🍰", range: "฿6-9" }
      ];

      return categories.map(cat => `
        <button class="category-card p-6 text-center cursor-pointer"
                style="margin: 0.25rem; border-color: ${selectedCategory === cat.key ? "#e74c3c" : "transparent"};"
                onclick="selectCategory('${cat.key}')">
          <div style="font-size: 2.4rem;" class="mb-2">${cat.emoji}</div>
          <h3 class="font-semibold" style="font-size: 1rem;">${cat.key}</h3>
          <p style="font-size: 0.85rem; color: #7f8c8d;">${cat.range}</p>
        </button>
      `).join("");
    }

    // ----------------- เรนเดอร์เมนู -----------------
    function renderMenuItems() {
      const container = document.getElementById("menuItems");
      if (!container) return;

      if (!selectedCategory) {
        container.innerHTML = `
          <div class="text-center py-8" style="color: #7f8c8d;">
            <div style="font-size: 3rem;" class="mb-4">👆</div>
            <p style="font-size: 1.1rem;">เลือกหมวดหมู่เพื่อดูเมนู</p>
          </div>
        `;
        return;
      }

      const items = menuData[selectedCategory] || [];

      container.innerHTML = items.map(item => {
        const isInOrder = orderItems.some(oi => oi.id === item.id);
        const price = item.price || item.basePrice;

        return `
          <div class="menu-item p-5 cursor-pointer mb-4 ${isInOrder ? "item-selected" : ""}"
               style="border: 2px solid ${isInOrder ? "#e74c3c" : "transparent"};"
               onclick="toggleItem(${item.id})">
            <div class="flex items-start gap-4">
              <div class="text-center" style="flex-shrink:0;">
                <div style="font-size: 2.4rem;" class="mb-1">${item.emoji}</div>
              </div>
              <div class="flex-grow">
                <div class="flex justify-between items-start mb-2">
                  <h3 class="font-semibold" style="font-size: 1.1rem;">${item.name}</h3>
                  <span class="font-bold" style="font-size: 1rem; color:#e74c3c;">
                    ฿${price}${item.basePrice ? "+" : ""}
                  </span>
                </div>
                <p style="font-size: 0.9rem; color:#7f8c8d;" class="mb-3">
                  ${item.description}
                </p>
                ${item.toppings ? `
                  <div id="toppings-${item.id}" class="hidden mt-3"
                       style="border-top:1px solid rgba(149,165,166,0.25); padding-top:0.75rem;">
                    <p class="font-semibold" style="font-size: 0.9rem;">เลือกท็อปปิ้ง:</p>
                    ${item.toppings.map((topping, idx) => `
                      <label class="flex items-center gap-2 cursor-pointer" onclick="event.stopPropagation()">
                        <input type="checkbox" id="topping-${item.id}-${idx}">
                        <span style="font-size: 0.9rem;">${topping.name} (+฿${topping.price})</span>
                      </label>
                    `).join("")}
                  </div>
                ` : ""}
              </div>
            </div>
          </div>
        `;
      }).join("");
    }

    // ----------------- เรนเดอร์ออเดอร์ -----------------
    function renderOrderList() {
      const container = document.getElementById("orderList");
      if (!container) return;

      if (orderItems.length === 0) {
        container.innerHTML = `
          <div class="text-center py-4" style="color: #7f8c8d;">
            <p style="font-size: 0.9rem;">ออเดอร์ของคุณว่างเปล่า</p>
          </div>
        `;
        return;
      }

      container.innerHTML = orderItems.map(item => {
        const toppingsText = item.selectedToppings && item.selectedToppings.length > 0
          ? `<div style="font-size: 0.75rem; color:#7f8c8d;" class="mt-1">
               ${item.selectedToppings.map(t => `• ${t.name} +฿${t.price}`).join("<br>")}
             </div>`
          : "";

        return `
          <div class="flex justify-between items-start gap-2 pb-3 border-bottom">
            <div class="flex-grow">
              <div style="font-size: 0.95rem;" class="font-semibold">${item.name}</div>
              ${toppingsText}
            </div>
            <div class="text-right" style="flex-shrink:0;">
              <div style="font-size: 0.95rem;" class="font-semibold">฿${item.totalPrice.toFixed(2)}</div>
              <button class="mt-1 px-2 py-1 btn-secondary"
                      style="border-radius:6px; font-size: 0.75rem;"
                      onclick="removeItem(${item.id})">
                ลบ
              </button>
            </div>
          </div>
        `;
      }).join("");
    }

    // ----------------- Modal เกมเงินทอน -----------------
    function renderPaymentModal() {
      const { total } = getTotal();
      const modal = document.createElement("div");
      modal.className = "modal-backdrop";
      modal.id = "paymentModal";

      let changeOptions = [];
      if (selectedPaymentAmount !== null) {
        changeOptions = generateChangeOptions(selectedPaymentAmount, total);
      }

      modal.innerHTML = `
        <div class="shadow-lg p-8 max-w-md w-full fade-in"
             style="background-color:#ffffff; border-radius:16px; max-height:90%; overflow-y:auto;">
          <div class="text-center mb-6">
            <h2 class="font-bold mb-2" style="font-size: 1.4rem;">🎮 เกมคำนวณเงินทอน</h2>
            <p style="font-size: 1rem; color:#7f8c8d;">เลือกธนบัตรที่จ่าย และเงินทอนที่ถูกต้อง</p>
          </div>

          <div class="mb-6 p-4 text-center"
               style="background-color: rgba(231,76,60,0.12); border-radius:12px;">
            <p style="font-size: 0.9rem; color:#7f8c8d;">ยอดที่ต้องจ่าย</p>
            <p class="font-bold" style="font-size: 1.8rem; color:#e74c3c;">
              ฿${total.toFixed(2)}
            </p>
          </div>

          <div class="mb-6">
            <p class="font-semibold mb-3">1️⃣ เลือกธนบัตรที่จ่าย:</p>
            <div class="grid" style="grid-template-columns: repeat(3, minmax(0,1fr)); gap:0.75rem;">
              ${[20, 50, 100, 500, 1000].map(amount => `
                <button class="py-3 px-2 rounded-lg font-bold"
                        style="
                          background-color: ${selectedPaymentAmount === amount ? '#e74c3c' : 'rgba(149,165,166,0.2)'};
                          color: ${selectedPaymentAmount === amount ? '#ffffff' : '#2c3e50'};
                          border: 2px solid ${selectedPaymentAmount === amount ? '#e74c3c' : 'transparent'};
                          cursor:pointer;
                        "
                        onclick="selectPayment(${amount})">
                  ฿${amount}
                </button>
              `).join("")}
            </div>
          </div>

          ${selectedPaymentAmount !== null ? `
            <div class="mb-6">
              <p class="font-semibold mb-3">2️⃣ เงินทอนที่ถูกต้องคือ:</p>
              <div class="grid" style="grid-template-columns: repeat(3, minmax(0,1fr)); gap:0.75rem;">
                ${changeOptions.map(amount => `
                  <button class="py-3 px-2 rounded-lg font-bold"
                          style="
                            background-color: ${selectedChangeAmount === amount ? '#e74c3c' : 'rgba(149,165,166,0.2)'};
                            color: ${selectedChangeAmount === amount ? '#ffffff' : '#2c3e50'};
                            border: 2px solid ${selectedChangeAmount === amount ? '#e74c3c' : 'transparent'};
                            cursor:pointer;
                          "
                          onclick="selectChange(${amount})">
                    ฿${amount.toFixed(2)}
                  </button>
                `).join("")}
              </div>
            </div>
          ` : ""}

          ${selectedPaymentAmount !== null && selectedChangeAmount !== null && !gameResult ? `
            <button class="btn btn-primary w-full mb-3" onclick="checkAnswer()">
              ✓ ตรวจคำตอบ
            </button>
          ` : ""}

          ${gameResult ? `
            <div class="text-center p-4 mb-4 fade-in"
                 style="background-color:${gameResult.correct ? 'rgba(76,175,80,0.12)' : 'rgba(244,67,54,0.12)'};
                        border:2px solid ${gameResult.correct ? '#4caf50' : '#f44336'};
                        border-radius:12px;">
              <div style="font-size: 2.4rem;">${gameResult.correct ? "⭐" : "❌"}</div>
              <p class="font-bold mt-2" style="font-size: 1.1rem; color:${gameResult.correct ? '#4caf50' : '#f44336'};">
                ${gameResult.correct ? "ถูกต้อง! คุณได้ 1 ดาว" : "ผิดนะ! ลองใหม่อีกครั้ง"}
              </p>
              <p style="font-size: 0.95rem; color:#7f8c8d; margin-top: 8px;">
                ${gameResult.message}
              </p>
            </div>
          ` : ""}

          <button class="btn w-full"
                  style="background-color: rgba(149,165,166,0.2);"
                  onclick="closePaymentGame()">
            ${gameResult && gameResult.correct ? "เริ่มเกมใหม่" : "ปิด"}
          </button>
        </div>
      `;

      document.body.appendChild(modal);
    }

    // ----------------- Event handlers -----------------
    window.selectCategory = function(category) {
      selectedCategory = category;
      orderItems = orderItems; // no-op แค่กันงง
      renderApp();
    };

    window.toggleItem = function(itemId) {
      const existingIndex = orderItems.findIndex(oi => oi.id === itemId);

      if (existingIndex >= 0) {
        orderItems.splice(existingIndex, 1);
        renderApp();
        return;
      }

      // หาเมนู
      let item;
      for (const cat in menuData) {
        const found = menuData[cat].find(i => i.id === itemId);
        if (found) {
          item = found;
          break;
        }
      }
      if (!item) return;

      if (item.toppings) {
        const toppingsDiv = document.getElementById(`toppings-${itemId}`);
        // ถ้ายังไม่เปิด เลือกให้เปิดก่อน
        if (toppingsDiv && toppingsDiv.classList.contains("hidden")) {
          toppingsDiv.classList.remove("hidden");
          return;
        }

        // ถ้าเปิดแล้ว แสดงว่าเป็นรอบที่ 2: เก็บท็อปปิ้ง แล้วเพิ่มออเดอร์
        const selectedToppings = [];
        item.toppings.forEach((topping, idx) => {
          const checkbox = document.getElementById(`topping-${item.id}-${idx}`);
          if (checkbox && checkbox.checked) {
            selectedToppings.push(topping);
          }
        });
        const totalPrice = item.basePrice + selectedToppings.reduce((sum, t) => sum + t.price, 0);

        orderItems.push({
          id: item.id,
          name: item.name,
          selectedToppings,
          totalPrice
        });
        renderApp();
      } else {
        // เมนูธรรมดา
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
      const modal = document.getElementById("paymentModal");
      if (modal) modal.remove();
      renderApp();
    };

    window.selectPayment = function(amount) {
      selectedPaymentAmount = amount;
      selectedChangeAmount = null;
      gameResult = null;
      const modal = document.getElementById("paymentModal");
      if (modal) modal.remove();
      renderPaymentModal();
    };

    window.selectChange = function(amount) {
      selectedChangeAmount = amount;
      const modal = document.getElementById("paymentModal");
      if (modal) modal.remove();
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

      const modal = document.getElementById("paymentModal");
      if (modal) modal.remove();
      renderPaymentModal();

      if (gameResult.correct) {
        setTimeout(() => {
          closePaymentGame();
        }, 2500);
      }
    };

    // ----------------- เริ่มต้น -----------------
    renderApp();
  </script>
</body>
</html>
