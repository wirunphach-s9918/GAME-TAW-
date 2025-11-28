<!DOCTYPE html>
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
        { id: 1, name: "Bruschetta", price: 8, emoji: "🍞", descri
