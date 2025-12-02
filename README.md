
<html lang="th">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Baby Smart Tracker</title>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="/_sdk/data_sdk.js"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&amp;display=swap" rel="stylesheet">
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      box-sizing: border-box;
      font-family: 'Sarabun', sans-serif !important;
    }
    
    * {
      font-family: 'Sarabun', sans-serif !important;
    }
    
    @keyframes float {
      0%, 100% { transform: translateY(0px); }
      50% { transform: translateY(-10px); }
    }

    .float-animation {
      animation: float 3s ease-in-out infinite;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-10px); }
      to { opacity: 1; transform: translateY(0); }
    }

    .fade-in {
      animation: fadeIn 0.3s ease-out;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.5; }
    }

    .pulse {
      animation: pulse 2s ease-in-out infinite;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
 </head>
 <body class="w-full h-full m-0 p-0">
  <div id="app" class="w-full min-h-full"></div>
  <script>
    const defaultConfig = {
      background_color: "#fff5f7",
      surface_color: "#fffbfc",
      text_color: "#4a3448",
      primary_action_color: "#ff9ebb",
      secondary_action_color: "#b4a7ff",
      font_family: "Sarabun",
      font_size: 16,
      app_title: "Baby Smart Tracker 👶",
      subtitle: "ระบบติดตามพัฒนาการและสุขภาพลูกน้อยอย่างครบวงจร"
    };

    let allData = [];
    let childProfile = null;
    let currentView = "profile";
    let isLoading = false;
    let activeTimer = null;
    let timerInterval = null;

    // Reference data for milestones
    const milestonesData = [
      {
        id: "0-3m-1",
        ageRange: "0-3 เดือน",
        category: "🏃 การเคลื่อนไหว",
        milestone: "ยกศีรษะขึ้นได้เมื่อนอนคว่ำ",
        description: "เมื่อนอนคว่ำ สามารถยกศีรษะขึ้นได้ 45-90 องศา"
      },
      {
        id: "0-3m-2",
        ageRange: "0-3 เดือน",
        category: "👀 สายตา",
        milestone: "มองตามวัตถุที่เคลื่อนไหว",
        description: "สามารถติดตามวัตถุสีสดใสหรือใบหน้าที่เคลื่อนที่ช้าๆ"
      },
      {
        id: "0-3m-3",
        ageRange: "0-3 เดือน",
        category: "😊 อารมณ์-สังคม",
        milestone: "ยิ้มเมื่อเห็นผู้ปกครอง",
        description: "ยิ้มตอบเมื่อเห็นใบหน้าคุ้นเคย"
      },
      {
        id: "4-6m-1",
        ageRange: "4-6 เดือน",
        category: "🏃 การเคลื่อนไหว",
        milestone: "พลิกตัวได้ทั้งสองทาง",
        description: "พลิกจากหงายเป็นคว่ำได้ และพลิกจากคว่ำเป็นหงายได้"
      },
      {
        id: "4-6m-2",
        ageRange: "4-6 เดือน",
        category: "✋ มือและนิ้ว",
        milestone: "หยิบของใส่ปากได้",
        description: "ใช้มือจับของเล่นและนำเข้าปากเพื่อสำรวจ"
      },
      {
        id: "7-9m-1",
        ageRange: "7-9 เดือน",
        category: "🏃 การเคลื่อนไหว",
        milestone: "นั่งตัวเองโดยไม่ต้องค้ำจุน",
        description: "นั่งได้มั่นคงโดยไม่ต้องใช้มือค้ำ"
      },
      {
        id: "7-9m-2",
        ageRange: "7-9 เดือน",
        category: "🏃 การเคลื่อนไหว",
        milestone: "คลานหรือคืบคลานได้",
        description: "คลานด้วยท้องแนบพื้น หรือคลานคุกเข่าได้"
      },
      {
        id: "10-12m-1",
        ageRange: "10-12 เดือน",
        category: "🏃 การเคลื่อนไหว",
        milestone: "ยืนได้โดยจับค้ำ",
        description: "ยืนได้โดยจับเฟอร์นิเจอร์"
      },
      {
        id: "10-12m-2",
        ageRange: "10-12 เดือน",
        category: "👂 การสื่อสาร",
        milestone: "พูดคำแรก (มา-มา, พ่อ-พ่อ)",
        description: "เริ่มพูดคำที่มีความหมายชัดเจน"
      }
    ];

    // Reference data for vaccines
    const vaccinesData = [
      {
        id: "birth-bcg",
        ageRange: "แรกเกิด",
        vaccineName: "BCG (วัณโรค)",
        description: "ป้องกันวัณโรครุนแรง ฉีดที่ต้นแขนซ้าย",
        sideEffects: "อาจมีตุ่มแดงที่จุดฉีด เป็นเรื่องปกติ"
      },
      {
        id: "2m-dtap1",
        ageRange: "2 เดือน",
        vaccineName: "DTaP-IPV-Hib-HB 1",
        description: "วัคซีนรวม 6 in 1 เข็มแรก",
        sideEffects: "อาจมีไข้เล็กน้อย 37.5-38°C, บวมแดงที่จุดฉีด"
      },
      {
        id: "4m-dtap2",
        ageRange: "4 เดือน",
        vaccineName: "DTaP-IPV-Hib-HB 2",
        description: "วัคซีนรวม 6 in 1 เข็มที่ 2",
        sideEffects: "อาจมีไข้เล็กน้อย, ง่วงนอน"
      },
      {
        id: "6m-dtap3",
        ageRange: "6 เดือน",
        vaccineName: "DTaP-IPV-Hib-HB 3",
        description: "วัคซีนรวม 6 in 1 เข็มที่ 3",
        sideEffects: "อาจมีไข้, หงุดหงิด 1-2 วัน"
      },
      {
        id: "9m-mmr1",
        ageRange: "9 เดือน",
        vaccineName: "MMR 1 (หัด-คางทูม-หัดเยอรมัน)",
        description: "ป้องกันหัด คางทูม หัดเยอรมัน เข็มแรก",
        sideEffects: "อาจมีไข้ 7-10 วันหลังฉีด, ผื่นแดงเล็กน้อย"
      }
    ];

    // Activity suggestions by age
    const activitySuggestions = {
      "0-3": [
        { icon: "👀", title: "Tummy Time", description: "ให้นอนคว่ำ 3-5 นาที เพื่อฝึกกล้ามเนื้อคอและหลัง", duration: "5-10 นาที" },
        { icon: "🪞", title: "Mirror Play", description: "ให้ส่องกระจกเพื่อกระตุ้นการรับรู้ใบหน้า", duration: "3-5 นาที" },
        { icon: "🎵", title: "เพลงกล่อม", description: "เปิดเพลงกล่อมเด็กเบาๆ เพื่อพัฒนาการได้ยิน", duration: "10-15 นาที" }
      ],
      "4-6": [
        { icon: "🧸", title: "Reach & Grab", description: "วางของเล่นให้เด็กหยิบเพื่อฝึกประสานมือตา", duration: "10 นาที" },
        { icon: "📚", title: "หนังสือผ้า", description: "อ่านหนังสือผ้าและให้สัมผัสพื้นผิว", duration: "5-10 นาที" },
        { icon: "🔊", title: "เสียงธรรมชาติ", description: "ให้ฟังเสียงนก น้ำไหล เพื่อกระตุ้นสมอง", duration: "5 นาที" }
      ],
      "7-12": [
        { icon: "🪣", title: "หยอดกระปุก", description: "ให้หยอดของเล่นเข้ากระปุกเพื่อฝึก Fine Motor", duration: "10-15 นาที" },
        { icon: "🏗️", title: "ซ้อนบล็อก", description: "ให้ซ้อนบล็อกนิ่มเพื่อฝึกประสานมือตา", duration: "10 นาที" },
        { icon: "👶", title: "Peek-a-boo", description: "เล่นซ่อนหน้าเพื่อสอน Object Permanence", duration: "5 นาที" }
      ]
    };

    const dataHandler = {
      onDataChanged(data) {
        allData = data;
        childProfile = data.find(item => item.type === "profile");
        renderApp();
      }
    };

    async function initializeApp() {
      const initResult = await window.dataSdk.init(dataHandler);
      if (!initResult.isOk) {
        console.error("Failed to initialize data SDK");
      }

      if (window.elementSdk) {
        window.elementSdk.init({
          defaultConfig,
          onConfigChange: async (config) => {
            renderApp();
          },
          mapToCapabilities: (config) => ({
            recolorables: [
              {
                get: () => config.background_color || defaultConfig.background_color,
                set: (value) => {
                  config.background_color = value;
                  window.elementSdk.setConfig({ background_color: value });
                }
              },
              {
                get: () => config.surface_color || defaultConfig.surface_color,
                set: (value) => {
                  config.surface_color = value;
                  window.elementSdk.setConfig({ surface_color: value });
                }
              },
              {
                get: () => config.text_color || defaultConfig.text_color,
                set: (value) => {
                  config.text_color = value;
                  window.elementSdk.setConfig({ text_color: value });
                }
              },
              {
                get: () => config.primary_action_color || defaultConfig.primary_action_color,
                set: (value) => {
                  config.primary_action_color = value;
                  window.elementSdk.setConfig({ primary_action_color: value });
                }
              },
              {
                get: () => config.secondary_action_color || defaultConfig.secondary_action_color,
                set: (value) => {
                  config.secondary_action_color = value;
                  window.elementSdk.setConfig({ secondary_action_color: value });
                }
              }
            ],
            borderables: [],
            fontEditable: {
              get: () => config.font_family || defaultConfig.font_family,
              set: (value) => {
                config.font_family = value;
                window.elementSdk.setConfig({ font_family: value });
              }
            },
            fontSizeable: {
              get: () => config.font_size || defaultConfig.font_size,
              set: (value) => {
                config.font_size = value;
                window.elementSdk.setConfig({ font_size: value });
              }
            }
          }),
          mapToEditPanelValues: (config) => new Map([
            ["app_title", config.app_title || defaultConfig.app_title],
            ["subtitle", config.subtitle || defaultConfig.subtitle]
          ])
        });
      }

      renderApp();
    }

    function calculateAge(birthdate) {
      const birth = new Date(birthdate);
      const today = new Date();
      
      let years = today.getFullYear() - birth.getFullYear();
      let months = today.getMonth() - birth.getMonth();
      
      if (months < 0) {
        years--;
        months += 12;
      }
      
      const totalMonths = years * 12 + months;
      
      if (years === 0) {
        return { text: `${months} เดือน`, months: totalMonths };
      } else if (months === 0) {
        return { text: `${years} ปี`, months: totalMonths };
      } else {
        return { text: `${years} ปี ${months} เดือน`, months: totalMonths };
      }
    }

    function getAgeGroup(months) {
      if (months <= 3) return "0-3";
      if (months <= 6) return "4-6";
      if (months <= 12) return "7-12";
      return "7-12";
    }

    function calculateParacetamolDose(weight) {
      // 10-15 mg/kg per dose, max 60 mg/kg/day
      const dosePerKg = 15; // mg
      const totalDose = weight * dosePerKg;
      const syrupConcentration = 120; // mg per 5 ml (standard)
      const mlDose = (totalDose / syrupConcentration) * 5;
      
      return {
        mgDose: totalDose.toFixed(1),
        mlDose: mlDose.toFixed(1)
      };
    }

    function showToast(message) {
      const toast = document.getElementById('toast-message');
      if (toast) {
        toast.textContent = message;
        toast.classList.remove('hidden');
        toast.style.opacity = '1';
        setTimeout(() => {
          toast.style.opacity = '0';
          setTimeout(() => toast.classList.add('hidden'), 300);
        }, 2500);
      }
    }

    async function saveChildProfile(event) {
      event.preventDefault();
      
      if (isLoading) return;
      
      const name = document.getElementById('child-name').value.trim();
      const birthdate = document.getElementById('child-birthdate').value;
      const gender = document.getElementById('child-gender').value;
      const weight = parseFloat(document.getElementById('child-weight').value) || 0;
      const height = parseFloat(document.getElementById('child-height').value) || 0;
      
      if (!name || !birthdate || !gender) {
        showToast("กรุณากรอกข้อมูลให้ครบถ้วน");
        return;
      }

      isLoading = true;
      
      if (childProfile) {
        const result = await window.dataSdk.update({
          __backendId: childProfile.__backendId,
          type: "profile",
          child_name: name,
          child_birthdate: birthdate,
          child_gender: gender,
          child_weight: weight,
          child_height: height,
          activity_type: "",
          start_time: "",
          end_time: "",
          duration: 0,
          amount: 0,
          notes: "",
          milestone_id: "",
          completed: false,
          completed_date: "",
          vaccine_id: "",
          vaccine_date: "",
          temperature: 0,
          medicine_name: "",
          medicine_dose: 0,
          stool_color: "",
          stool_texture: ""
        });
        
        if (result.isOk) {
          showToast("✓ บันทึกข้อมูลเรียบร้อย");
          currentView = "dashboard";
        } else {
          showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
        }
      } else {
        if (allData.length >= 999) {
          showToast("ถึงขีดจำกัด 999 รายการ");
          isLoading = false;
          return;
        }
        
        const result = await window.dataSdk.create({
          type: "profile",
          child_name: name,
          child_birthdate: birthdate,
          child_gender: gender,
          child_weight: weight,
          child_height: height,
          activity_type: "",
          start_time: "",
          end_time: "",
          duration: 0,
          amount: 0,
          notes: "",
          milestone_id: "",
          completed: false,
          completed_date: "",
          vaccine_id: "",
          vaccine_date: "",
          temperature: 0,
          medicine_name: "",
          medicine_dose: 0,
          stool_color: "",
          stool_texture: ""
        });
        
        if (result.isOk) {
          showToast("✓ บันทึกข้อมูลเรียบร้อย");
          currentView = "dashboard";
        } else {
          showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
        }
      }
      
      isLoading = false;
    }

    async function startActivity(activityType) {
      if (isLoading || activeTimer) return;
      
      if (allData.length >= 999) {
        showToast("ถึงขีดจำกัด 999 รายการ");
        return;
      }

      const startTime = new Date().toISOString();
      
      isLoading = true;
      
      const result = await window.dataSdk.create({
        type: "activity",
        activity_type: activityType,
        start_time: startTime,
        end_time: "",
        duration: 0,
        amount: 0,
        notes: "",
        child_name: "",
        child_birthdate: "",
        child_gender: "",
        child_weight: 0,
        child_height: 0,
        milestone_id: "",
        completed: false,
        completed_date: "",
        vaccine_id: "",
        vaccine_date: "",
        temperature: 0,
        medicine_name: "",
        medicine_dose: 0,
        stool_color: "",
        stool_texture: ""
      });
      
      isLoading = false;
      
      if (result.isOk) {
        showToast(`✓ เริ่มบันทึก${activityType === 'sleep' ? 'การนอน' : activityType === 'feeding' ? 'การกิน' : 'การขับถ่าย'}`);
      } else {
        showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
      }
    }

    async function stopActivity(activity) {
      if (isLoading) return;

      const endTime = new Date().toISOString();
      const startTime = new Date(activity.start_time);
      const duration = Math.floor((new Date(endTime) - startTime) / 60000); // minutes

      isLoading = true;

      const result = await window.dataSdk.update({
        __backendId: activity.__backendId,
        type: "activity",
        activity_type: activity.activity_type,
        start_time: activity.start_time,
        end_time: endTime,
        duration: duration,
        amount: activity.amount || 0,
        notes: activity.notes || "",
        child_name: "",
        child_birthdate: "",
        child_gender: "",
        child_weight: 0,
        child_height: 0,
        milestone_id: "",
        completed: false,
        completed_date: "",
        vaccine_id: "",
        vaccine_date: "",
        temperature: 0,
        medicine_name: "",
        medicine_dose: 0,
        stool_color: "",
        stool_texture: ""
      });

      isLoading = false;

      if (result.isOk) {
        showToast(`✓ บันทึกเสร็จสิ้น (${duration} นาที)`);
      } else {
        showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
      }
    }

    async function logHealth(event) {
      event.preventDefault();
      
      if (isLoading) return;
      
      if (allData.length >= 999) {
        showToast("ถึงขีดจำกัด 999 รายการ");
        return;
      }

      const temperature = parseFloat(document.getElementById('temperature').value) || 0;
      const medicineName = document.getElementById('medicine-name').value.trim();
      const medicineDose = parseFloat(document.getElementById('medicine-dose').value) || 0;
      const stoolColor = document.getElementById('stool-color').value;
      const stoolTexture = document.getElementById('stool-texture').value;
      const healthNotes = document.getElementById('health-notes').value.trim();

      isLoading = true;

      const result = await window.dataSdk.create({
        type: "health",
        temperature: temperature,
        medicine_name: medicineName,
        medicine_dose: medicineDose,
        stool_color: stoolColor,
        stool_texture: stoolTexture,
        notes: healthNotes,
        start_time: new Date().toISOString(),
        child_name: "",
        child_birthdate: "",
        child_gender: "",
        child_weight: 0,
        child_height: 0,
        activity_type: "",
        end_time: "",
        duration: 0,
        amount: 0,
        milestone_id: "",
        completed: false,
        completed_date: "",
        vaccine_id: "",
        vaccine_date: ""
      });

      isLoading = false;

      if (result.isOk) {
        showToast("✓ บันทึกข้อมูลสุขภาพเรียบร้อย");
        document.getElementById('health-form').reset();
      } else {
        showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
      }
    }

    async function toggleMilestone(milestoneId) {
      if (isLoading) return;

      const existing = allData.find(item => item.milestone_id === milestoneId);
      
      if (existing) {
        isLoading = true;
        
        const result = await window.dataSdk.update({
          __backendId: existing.__backendId,
          type: "milestone",
          milestone_id: existing.milestone_id,
          completed: !existing.completed,
          completed_date: !existing.completed ? new Date().toISOString() : existing.completed_date,
          notes: existing.notes || "",
          child_name: "",
          child_birthdate: "",
          child_gender: "",
          child_weight: 0,
          child_height: 0,
          activity_type: "",
          start_time: "",
          end_time: "",
          duration: 0,
          amount: 0,
          vaccine_id: "",
          vaccine_date: "",
          temperature: 0,
          medicine_name: "",
          medicine_dose: 0,
          stool_color: "",
          stool_texture: ""
        });
        
        isLoading = false;
        
        if (!result.isOk) {
          showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
        }
      } else {
        if (allData.length >= 999) {
          showToast("ถึงขีดจำกัด 999 รายการ");
          return;
        }

        isLoading = true;
        
        const result = await window.dataSdk.create({
          type: "milestone",
          milestone_id: milestoneId,
          completed: true,
          completed_date: new Date().toISOString(),
          notes: "",
          child_name: "",
          child_birthdate: "",
          child_gender: "",
          child_weight: 0,
          child_height: 0,
          activity_type: "",
          start_time: "",
          end_time: "",
          duration: 0,
          amount: 0,
          vaccine_id: "",
          vaccine_date: "",
          temperature: 0,
          medicine_name: "",
          medicine_dose: 0,
          stool_color: "",
          stool_texture: ""
        });
        
        isLoading = false;
        
        if (!result.isOk) {
          showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
        }
      }
    }

    async function saveVaccineDate(vaccineId, date) {
      if (isLoading) return;

      const existing = allData.find(item => item.vaccine_id === vaccineId);
      
      if (existing) {
        isLoading = true;
        
        const result = await window.dataSdk.update({
          __backendId: existing.__backendId,
          type: "vaccine",
          vaccine_id: vaccineId,
          vaccine_date: date,
          milestone_id: "",
          completed: false,
          completed_date: "",
          notes: "",
          child_name: "",
          child_birthdate: "",
          child_gender: "",
          child_weight: 0,
          child_height: 0,
          activity_type: "",
          start_time: "",
          end_time: "",
          duration: 0,
          amount: 0,
          temperature: 0,
          medicine_name: "",
          medicine_dose: 0,
          stool_color: "",
          stool_texture: ""
        });
        
        isLoading = false;
        
        if (result.isOk) {
          showToast("✓ บันทึกวันที่ฉีดวัคซีนแล้ว");
        } else {
          showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
        }
      } else {
        if (allData.length >= 999) {
          showToast("ถึงขีดจำกัด 999 รายการ");
          return;
        }

        isLoading = true;
        
        const result = await window.dataSdk.create({
          type: "vaccine",
          vaccine_id: vaccineId,
          vaccine_date: date,
          milestone_id: "",
          completed: false,
          completed_date: "",
          notes: "",
          child_name: "",
          child_birthdate: "",
          child_gender: "",
          child_weight: 0,
          child_height: 0,
          activity_type: "",
          start_time: "",
          end_time: "",
          duration: 0,
          amount: 0,
          temperature: 0,
          medicine_name: "",
          medicine_dose: 0,
          stool_color: "",
          stool_texture: ""
        });
        
        isLoading = false;
        
        if (result.isOk) {
          showToast("✓ บันทึกวันที่ฉีดวัคซีนแล้ว");
        } else {
          showToast("เกิดข้อผิดพลาด กรุณาลองใหม่");
        }
      }
    }

    function renderApp() {
      if (currentView === "profile") {
        renderProfileView();
      } else if (currentView === "dashboard") {
        renderDashboardView();
      } else if (currentView === "tracker") {
        renderTrackerView();
      } else if (currentView === "health") {
        renderHealthView();
      } else if (currentView === "milestone") {
        renderMilestoneView();
      } else if (currentView === "vaccine") {
        renderVaccineView();
      } else if (currentView === "activities") {
        renderActivitiesView();
      }
    }

    function renderProfileView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;
      const appTitle = config.app_title || defaultConfig.app_title;
      const subtitle = config.subtitle || defaultConfig.subtitle;

      const profileName = childProfile?.child_name || '';
      const profileBirthdate = childProfile?.child_birthdate || '';
      const profileGender = childProfile?.child_gender || '';
      const profileWeight = childProfile?.child_weight || '';
      const profileHeight = childProfile?.child_height || '';

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 24px;">
          <header style="text-align: center; margin-bottom: 32px;">
            <div style="font-size: 48px; margin-bottom: 12px;">👶</div>
            <h1 style="color: ${textColor}; font-size: ${baseSize * 2}px; font-weight: 700; margin: 8px 0;">${appTitle}</h1>
            <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.9}px; margin: 0;">${subtitle}</p>
          </header>

          <main style="max-width: 600px; margin: 0 auto;">
            <div style="background: ${surfaceColor}; padding: 32px; border-radius: 16px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
              <h2 style="color: ${textColor}; font-size: ${baseSize * 1.5}px; font-weight: 600; margin: 0 0 24px 0;">ข้อมูลลูกน้อย</h2>
              
              <form id="profile-form">
                <div style="margin-bottom: 20px;">
                  <label for="child-name" style="display: block; color: ${textColor}; font-size: ${baseSize}px; font-weight: 500; margin-bottom: 8px;">ชื่อ-นามสกุล</label>
                  <input type="text" id="child-name" value="${profileName}" placeholder="กรอกชื่อลูกน้อย" style="width: 100%; padding: 12px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;" required>
                </div>

                <div style="margin-bottom: 20px;">
                  <label for="child-birthdate" style="display: block; color: ${textColor}; font-size: ${baseSize}px; font-weight: 500; margin-bottom: 8px;">วันเกิด</label>
                  <input type="date" id="child-birthdate" value="${profileBirthdate}" style="width: 100%; padding: 12px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;" required>
                </div>

                <div style="margin-bottom: 20px;">
                  <label for="child-gender" style="display: block; color: ${textColor}; font-size: ${baseSize}px; font-weight: 500; margin-bottom: 8px;">เพศ</label>
                  <select id="child-gender" style="width: 100%; padding: 12px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;" required>
                    <option value="">เลือกเพศ</option>
                    <option value="male" ${profileGender === 'male' ? 'selected' : ''}>ชาย</option>
                    <option value="female" ${profileGender === 'female' ? 'selected' : ''}>หญิง</option>
                  </select>
                </div>

                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 24px;">
                  <div>
                    <label for="child-weight" style="display: block; color: ${textColor}; font-size: ${baseSize}px; font-weight: 500; margin-bottom: 8px;">น้ำหนัก (กก.)</label>
                    <input type="number" step="0.1" id="child-weight" value="${profileWeight}" placeholder="3.5" style="width: 100%; padding: 12px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
                  </div>
                  <div>
                    <label for="child-height" style="display: block; color: ${textColor}; font-size: ${baseSize}px; font-weight: 500; margin-bottom: 8px;">ส่วนสูง (ซม.)</label>
                    <input type="number" step="0.1" id="child-height" value="${profileHeight}" placeholder="50" style="width: 100%; padding: 12px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
                  </div>
                </div>

                <button type="submit" style="width: 100%; padding: 14px; background: ${primaryColor}; color: white; border: none; border-radius: 8px; font-size: ${baseSize * 1.1}px; font-weight: 600; cursor: pointer; font-family: 'Sarabun', sans-serif;">
                  ${childProfile ? 'บันทึกการแก้ไข' : 'เริ่มต้นใช้งาน'}
                </button>
              </form>
            </div>

            ${childProfile ? `
              <button onclick="currentView='dashboard'; renderApp();" style="width: 100%; margin-top: 16px; padding: 14px; background: ${secondaryColor}; color: white; border: none; border-radius: 8px; font-size: ${baseSize * 1.1}px; font-weight: 600; cursor: pointer; font-family: 'Sarabun', sans-serif;">
                ไปที่หน้าหลัก →
              </button>
            ` : ''}
          </main>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;

      document.getElementById('profile-form').addEventListener('submit', saveChildProfile);
    }

    function renderDashboardView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      const ageInfo = childProfile ? calculateAge(childProfile.child_birthdate) : null;
      const ageGroup = ageInfo ? getAgeGroup(ageInfo.months) : "0-3";
      const suggestions = activitySuggestions[ageGroup] || activitySuggestions["0-3"];

      const todayActivities = allData.filter(item => {
        if (item.type !== "activity") return false;
        const itemDate = new Date(item.start_time);
        const today = new Date();
        return itemDate.toDateString() === today.toDateString();
      });

      const sleepCount = todayActivities.filter(a => a.activity_type === "sleep" && a.end_time).length;
      const feedCount = todayActivities.filter(a => a.activity_type === "feeding" && a.end_time).length;
      const diaperCount = todayActivities.filter(a => a.activity_type === "diaper" && a.end_time).length;

      const activeMilestones = milestonesData.filter(m => {
        const record = allData.find(d => d.milestone_id === m.id);
        return record && record.completed;
      }).length;

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 20px;">
          
          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; margin-bottom: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
            <div style="display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 12px;">
              <div>
                <h2 style="color: ${textColor}; font-size: ${baseSize * 1.3}px; margin: 0 0 4px 0; font-weight: 600;">สวัสดี ${childProfile?.child_name || 'ลูกน้อย'} 👶</h2>
                ${ageInfo ? `<p style="color: ${textColor}; opacity: 0.7; margin: 0; font-size: ${baseSize * 0.9}px;">อายุ: ${ageInfo.text}</p>` : ''}
              </div>
              <button onclick="currentView='profile'; renderApp();" style="padding: 8px 16px; background: ${secondaryColor}; color: white; border: none; border-radius: 6px; font-size: ${baseSize * 0.85}px; cursor: pointer; font-family: 'Sarabun', sans-serif;">แก้ไขข้อมูล</button>
            </div>
          </div>

          <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); gap: 12px; margin-bottom: 24px;">
            <div style="background: ${surfaceColor}; padding: 16px; border-radius: 12px; text-align: center; box-shadow: 0 2px 6px rgba(0,0,0,0.08);">
              <div style="font-size: 32px; margin-bottom: 8px;">😴</div>
              <p style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0;">${sleepCount}</p>
              <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.8}px; margin: 4px 0 0 0;">รอบนอนวันนี้</p>
            </div>
            
            <div style="background: ${surfaceColor}; padding: 16px; border-radius: 12px; text-align: center; box-shadow: 0 2px 6px rgba(0,0,0,0.08);">
              <div style="font-size: 32px; margin-bottom: 8px;">🍼</div>
              <p style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0;">${feedCount}</p>
              <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.8}px; margin: 4px 0 0 0;">มื้อวันนี้</p>
            </div>

            <div style="background: ${surfaceColor}; padding: 16px; border-radius: 12px; text-align: center; box-shadow: 0 2px 6px rgba(0,0,0,0.08);">
              <div style="font-size: 32px; margin-bottom: 8px;">🚼</div>
              <p style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0;">${diaperCount}</p>
              <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.8}px; margin: 4px 0 0 0;">ครั้งวันนี้</p>
            </div>

            <div style="background: ${surfaceColor}; padding: 16px; border-radius: 12px; text-align: center; box-shadow: 0 2px 6px rgba(0,0,0,0.08);">
              <div style="font-size: 32px; margin-bottom: 8px;">🎯</div>
              <p style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0;">${activeMilestones}</p>
              <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.8}px; margin: 4px 0 0 0;">พัฒนาการ</p>
            </div>
          </div>

          <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 12px; margin-bottom: 24px;">
            <button onclick="currentView='tracker'; renderApp();" style="background: ${primaryColor}; color: white; padding: 20px; border: none; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1); transition: transform 0.2s;" onmouseover="this.style.transform='translateY(-2px)'" onmouseout="this.style.transform='translateY(0)'">
              <div style="font-size: 36px; margin-bottom: 8px;">⏱️</div>
              <h3 style="font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0 0 4px 0;">Smart Tracker</h3>
              <p style="font-size: ${baseSize * 0.85}px; opacity: 0.9; margin: 0;">บันทึกนอน/กิน/ถ่าย</p>
            </button>

            <button onclick="currentView='health'; renderApp();" style="background: ${secondaryColor}; color: white; padding: 20px; border: none; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1); transition: transform 0.2s;" onmouseover="this.style.transform='translateY(-2px)'" onmouseout="this.style.transform='translateY(0)'">
              <div style="font-size: 36px; margin-bottom: 8px;">🏥</div>
              <h3 style="font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0 0 4px 0;">สมุดสุขภาพ</h3>
              <p style="font-size: ${baseSize * 0.85}px; opacity: 0.9; margin: 0;">บันทึกไข้/ยา/ถ่าย</p>
            </button>

            <button onclick="currentView='milestone'; renderApp();" style="background: linear-gradient(135deg, ${primaryColor}, ${secondaryColor}); color: white; padding: 20px; border: none; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1); transition: transform 0.2s;" onmouseover="this.style.transform='translateY(-2px)'" onmouseout="this.style.transform='translateY(0)'">
              <div style="font-size: 36px; margin-bottom: 8px;">📋</div>
              <h3 style="font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0 0 4px 0;">พัฒนาการ</h3>
              <p style="font-size: ${baseSize * 0.85}px; opacity: 0.9; margin: 0;">ติดตาม Milestones</p>
            </button>

            <button onclick="currentView='vaccine'; renderApp();" style="background: ${surfaceColor}; color: ${textColor}; padding: 20px; border: 2px solid ${primaryColor}; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1); transition: transform 0.2s;" onmouseover="this.style.transform='translateY(-2px)'" onmouseout="this.style.transform='translateY(0)'">
              <div style="font-size: 36px; margin-bottom: 8px;">💉</div>
              <h3 style="font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0 0 4px 0;">ตารางวัคซีน</h3>
              <p style="font-size: ${baseSize * 0.85}px; opacity: 0.7; margin: 0;">เตือนนัดฉีด</p>
            </button>

            <button onclick="currentView='activities'; renderApp();" style="background: ${surfaceColor}; color: ${textColor}; padding: 20px; border: 2px solid ${secondaryColor}; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1); transition: transform 0.2s;" onmouseover="this.style.transform='translateY(-2px)'" onmouseout="this.style.transform='translateY(0)'">
              <div style="font-size: 36px; margin-bottom: 8px;">🎨</div>
              <h3 style="font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0 0 4px 0;">กิจกรรม</h3>
              <p style="font-size: ${baseSize * 0.85}px; opacity: 0.7; margin: 0;">แนะนำเล่นอะไรดี</p>
            </button>
          </div>

          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.08);">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 16px 0;">🎯 กิจกรรมแนะนำวันนี้</h3>
            <div style="display: flex; flex-direction: column; gap: 12px;">
              ${suggestions.map(activity => `
                <div style="background: linear-gradient(135deg, ${primaryColor}10, ${secondaryColor}10); padding: 16px; border-radius: 10px; border-left: 4px solid ${primaryColor};">
                  <div style="display: flex; align-items: start; gap: 12px;">
                    <div style="font-size: 36px;">${activity.icon}</div>
                    <div style="flex: 1;">
                      <h4 style="color: ${textColor}; font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0 0 6px 0;">${activity.title}</h4>
                      <p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.9}px; margin: 0 0 6px 0;">${activity.description}</p>
                      <span style="background: ${primaryColor}; color: white; padding: 4px 10px; border-radius: 12px; font-size: ${baseSize * 0.75}px; display: inline-block;">${activity.duration}</span>
                    </div>
                  </div>
                </div>
              `).join('')}
            </div>
          </div>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;
    }

    function renderTrackerView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      const activeActivities = allData.filter(item => item.type === "activity" && !item.end_time);
      const recentActivities = allData
        .filter(item => item.type === "activity" && item.end_time)
        .sort((a, b) => new Date(b.end_time) - new Date(a.end_time))
        .slice(0, 10);

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 20px;">
          
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <button onclick="currentView='dashboard'; renderApp();" style="padding: 10px 16px; background: ${surfaceColor}; color: ${textColor}; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; cursor: pointer; font-family: 'Sarabun', sans-serif;">← กลับ</button>
            <h2 style="color: ${textColor}; font-size: ${baseSize * 1.5}px; font-weight: 600; margin: 0;">⏱️ Smart Tracker</h2>
            <div style="width: 80px;"></div>
          </div>

          <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 12px; margin-bottom: 24px;">
            <button onclick="startActivity('sleep')" style="background: ${primaryColor}; color: white; padding: 20px; border: none; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1);" ${isLoading || activeActivities.some(a => a.activity_type === 'sleep') ? 'disabled' : ''}>
              <div style="font-size: 40px; margin-bottom: 8px;">😴</div>
              <p style="font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0;">เริ่มนอน</p>
            </button>

            <button onclick="startActivity('feeding')" style="background: ${secondaryColor}; color: white; padding: 20px; border: none; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1);" ${isLoading || activeActivities.some(a => a.activity_type === 'feeding') ? 'disabled' : ''}>
              <div style="font-size: 40px; margin-bottom: 8px;">🍼</div>
              <p style="font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0;">เริ่มกิน</p>
            </button>

            <button onclick="startActivity('diaper')" style="background: linear-gradient(135deg, ${primaryColor}, ${secondaryColor}); color: white; padding: 20px; border: none; border-radius: 12px; cursor: pointer; font-family: 'Sarabun', sans-serif; box-shadow: 0 2px 6px rgba(0,0,0,0.1);" ${isLoading || activeActivities.some(a => a.activity_type === 'diaper') ? 'disabled' : ''}>
              <div style="font-size: 40px; margin-bottom: 8px;">🚼</div>
              <p style="font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0;">เปลี่ยนผ้าอ้อม</p>
            </button>
          </div>

          ${activeActivities.length > 0 ? `
            <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; margin-bottom: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); border: 3px solid ${primaryColor};">
              <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 16px 0;">กำลังดำเนินการ...</h3>
              ${activeActivities.map(activity => {
                const startTime = new Date(activity.start_time);
                const elapsed = Math.floor((new Date() - startTime) / 60000);
                const activityName = activity.activity_type === 'sleep' ? 'นอน' : activity.activity_type === 'feeding' ? 'กิน' : 'เปลี่ยนผ้าอ้อม';
                const activityIcon = activity.activity_type === 'sleep' ? '😴' : activity.activity_type === 'feeding' ? '🍼' : '🚼';
                
                return `
                  <div style="background: white; padding: 16px; border-radius: 10px; margin-bottom: 12px;">
                    <div style="display: flex; justify-content: space-between; align-items: center; gap: 16px;">
                      <div style="flex: 1;">
                        <div style="display: flex; align-items: center; gap: 12px; margin-bottom: 8px;">
                          <span style="font-size: 32px;">${activityIcon}</span>
                          <div>
                            <h4 style="color: ${textColor}; font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0;">${activityName}</h4>
                            <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 4px 0 0 0;">เริ่ม: ${startTime.toLocaleTimeString('th-TH', { hour: '2-digit', minute: '2-digit' })}</p>
                          </div>
                        </div>
                        <p class="pulse" style="color: ${primaryColor}; font-size: ${baseSize * 1.5}px; font-weight: 700; margin: 0;">${elapsed} นาที</p>
                      </div>
                      <button onclick="stopActivity(${JSON.stringify(activity).replace(/"/g, '&quot;')})" style="padding: 12px 24px; background: ${primaryColor}; color: white; border: none; border-radius: 8px; font-size: ${baseSize}px; font-weight: 600; cursor: pointer; font-family: 'Sarabun', sans-serif;" ${isLoading ? 'disabled' : ''}>
                        หยุด
                      </button>
                    </div>
                  </div>
                `;
              }).join('')}
            </div>
          ` : ''}

          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.08);">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 16px 0;">ประวัติการบันทึก</h3>
            ${recentActivities.length === 0 ? `
              <p style="color: ${textColor}; opacity: 0.6; text-align: center; padding: 40px 20px; margin: 0;">ยังไม่มีการบันทึก</p>
            ` : `
              <div style="display: flex; flex-direction: column; gap: 10px;">
                ${recentActivities.map(activity => {
                  const startTime = new Date(activity.start_time);
                  const endTime = new Date(activity.end_time);
                  const activityName = activity.activity_type === 'sleep' ? 'นอน' : activity.activity_type === 'feeding' ? 'กิน' : 'เปลี่ยนผ้าอ้อม';
                  const activityIcon = activity.activity_type === 'sleep' ? '😴' : activity.activity_type === 'feeding' ? '🍼' : '🚼';
                  
                  return `
                    <div style="background: white; padding: 14px; border-radius: 10px; display: flex; justify-content: space-between; align-items: center;">
                      <div style="display: flex; align-items: center; gap: 12px;">
                        <span style="font-size: 28px;">${activityIcon}</span>
                        <div>
                          <p style="color: ${textColor}; font-size: ${baseSize * 0.95}px; font-weight: 600; margin: 0 0 4px 0;">${activityName}</p>
                          <p style="color: ${textColor}; opacity: 0.6; font-size: ${baseSize * 0.8}px; margin: 0;">${startTime.toLocaleTimeString('th-TH', { hour: '2-digit', minute: '2-digit' })} - ${endTime.toLocaleTimeString('th-TH', { hour: '2-digit', minute: '2-digit' })}</p>
                        </div>
                      </div>
                      <span style="background: ${primaryColor}20; color: ${primaryColor}; padding: 6px 12px; border-radius: 16px; font-size: ${baseSize * 0.85}px; font-weight: 600;">${activity.duration} นาที</span>
                    </div>
                  `;
                }).join('')}
              </div>
            `}
          </div>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;

      // Start updating active timers
      if (activeActivities.length > 0 && !timerInterval) {
        timerInterval = setInterval(() => {
          renderApp();
        }, 30000); // Update every 30 seconds
      } else if (activeActivities.length === 0 && timerInterval) {
        clearInterval(timerInterval);
        timerInterval = null;
      }
    }

    function renderHealthView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      const weight = childProfile?.child_weight || 0;
      const paracetamolDose = weight > 0 ? calculateParacetamolDose(weight) : null;

      const healthRecords = allData
        .filter(item => item.type === "health")
        .sort((a, b) => new Date(b.start_time) - new Date(a.start_time))
        .slice(0, 10);

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 20px;">
          
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <button onclick="currentView='dashboard'; renderApp();" style="padding: 10px 16px; background: ${surfaceColor}; color: ${textColor}; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; cursor: pointer; font-family: 'Sarabun', sans-serif;">← กลับ</button>
            <h2 style="color: ${textColor}; font-size: ${baseSize * 1.5}px; font-weight: 600; margin: 0;">🏥 สมุดสุขภาพ</h2>
            <div style="width: 80px;"></div>
          </div>

          ${paracetamolDose ? `
            <div style="background: linear-gradient(135deg, ${primaryColor}20, ${secondaryColor}20); padding: 16px; border-radius: 12px; margin-bottom: 20px; border-left: 4px solid ${primaryColor};">
              <h3 style="color: ${textColor}; font-size: ${baseSize * 1.1}px; font-weight: 600; margin: 0 0 12px 0;">💊 คำนวณยาพาราฯ (น้ำหนัก ${weight} กก.)</h3>
              <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 12px;">
                <div style="background: white; padding: 12px; border-radius: 8px;">
                  <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 0 0 4px 0;">ขนาดยา (มก.)</p>
                  <p style="color: ${primaryColor}; font-size: ${baseSize * 1.5}px; font-weight: 700; margin: 0;">${paracetamolDose.mgDose} มก.</p>
                </div>
                <div style="background: white; padding: 12px; border-radius: 8px;">
                  <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 0 0 4px 0;">ขนาดยา (มล.)</p>
                  <p style="color: ${primaryColor}; font-size: ${baseSize * 1.5}px; font-weight: 700; margin: 0;">${paracetamolDose.mlDose} มล.</p>
                </div>
              </div>
              <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.75}px; margin: 12px 0 0 0; text-align: center;">* คำนวณจาก 15 มก./กก. ทุก 4-6 ชม. (ยาน้ำ 120 มก./5 มล.)</p>
            </div>
          ` : ''}

          <div style="background: ${surfaceColor}; padding: 24px; border-radius: 12px; margin-bottom: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 20px 0;">บันทึกข้อมูลสุขภาพ</h3>
            
            <form id="health-form">
              <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 16px;">
                <div>
                  <label for="temperature" style="display: block; color: ${textColor}; font-size: ${baseSize * 0.9}px; font-weight: 500; margin-bottom: 6px;">🌡️ อุณหภูมิ (°C)</label>
                  <input type="number" step="0.1" id="temperature" placeholder="37.5" style="width: 100%; padding: 10px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
                </div>
                <div>
                  <label for="medicine-dose" style="display: block; color: ${textColor}; font-size: ${baseSize * 0.9}px; font-weight: 500; margin-bottom: 6px;">💧 ขนาดยา (มล.)</label>
                  <input type="number" step="0.1" id="medicine-dose" placeholder="2.5" style="width: 100%; padding: 10px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
                </div>
              </div>

              <div style="margin-bottom: 16px;">
                <label for="medicine-name" style="display: block; color: ${textColor}; font-size: ${baseSize * 0.9}px; font-weight: 500; margin-bottom: 6px;">💊 ชื่อยา</label>
                <input type="text" id="medicine-name" placeholder="เช่น พาราเซตามอล" style="width: 100%; padding: 10px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
              </div>

              <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 16px;">
                <div>
                  <label for="stool-color" style="display: block; color: ${textColor}; font-size: ${baseSize * 0.9}px; font-weight: 500; margin-bottom: 6px;">🎨 สีอุจจาระ</label>
                  <select id="stool-color" style="width: 100%; padding: 10px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
                    <option value="">ไม่ระบุ</option>
                    <option value="yellow">เหลือง (ปกติ)</option>
                    <option value="green">เขียว (นมส่วนหน้า)</option>
                    <option value="brown">น้ำตาล (ปกติ)</option>
                    <option value="red">แดง (มีเลือด-ควรพบแพทย์)</option>
                    <option value="black">ดำ (ควรพบแพทย์)</option>
                  </select>
                </div>
                <div>
                  <label for="stool-texture" style="display: block; color: ${textColor}; font-size: ${baseSize * 0.9}px; font-weight: 500; margin-bottom: 6px;">💧 ลักษณะ</label>
                  <select id="stool-texture" style="width: 100%; padding: 10px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white;">
                    <option value="">ไม่ระบุ</option>
                    <option value="soft">อ่อนนุ่ม (ปกติ)</option>
                    <option value="watery">เหลว (ท้องเสีย)</option>
                    <option value="hard">แข็ง (ท้องผูก)</option>
                    <option value="mucus">มีเมือก</option>
                  </select>
                </div>
              </div>

              <div style="margin-bottom: 20px;">
                <label for="health-notes" style="display: block; color: ${textColor}; font-size: ${baseSize * 0.9}px; font-weight: 500; margin-bottom: 6px;">📝 หมายเหตุ</label>
                <textarea id="health-notes" placeholder="บันทึกอาการเพิ่มเติม..." style="width: 100%; padding: 10px; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; font-family: 'Sarabun', sans-serif; color: ${textColor}; background: white; min-height: 80px;"></textarea>
              </div>

              <button type="submit" style="width: 100%; padding: 14px; background: ${primaryColor}; color: white; border: none; border-radius: 8px; font-size: ${baseSize * 1.05}px; font-weight: 600; cursor: pointer; font-family: 'Sarabun', sans-serif;">
                บันทึกข้อมูลสุขภาพ
              </button>
            </form>
          </div>

          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.08);">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 16px 0;">ประวัติการบันทึก</h3>
            ${healthRecords.length === 0 ? `
              <p style="color: ${textColor}; opacity: 0.6; text-align: center; padding: 40px 20px; margin: 0;">ยังไม่มีการบันทึก</p>
            ` : `
              <div style="display: flex; flex-direction: column; gap: 12px;">
                ${healthRecords.map(record => {
                  const recordTime = new Date(record.start_time);
                  
                  return `
                    <div style="background: white; padding: 16px; border-radius: 10px;">
                      <div style="display: flex; justify-content: space-between; align-items: start; margin-bottom: 10px;">
                        <p style="color: ${textColor}; font-size: ${baseSize * 0.95}px; font-weight: 600; margin: 0;">${recordTime.toLocaleDateString('th-TH')} ${recordTime.toLocaleTimeString('th-TH', { hour: '2-digit', minute: '2-digit' })}</p>
                        ${record.temperature > 0 ? `<span style="background: ${record.temperature >= 38 ? '#ff4444' : primaryColor}20; color: ${record.temperature >= 38 ? '#ff4444' : primaryColor}; padding: 4px 10px; border-radius: 12px; font-size: ${baseSize * 0.8}px; font-weight: 600;">🌡️ ${record.temperature}°C</span>` : ''}
                      </div>
                      ${record.medicine_name ? `<p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.85}px; margin: 4px 0;">💊 ${record.medicine_name} ${record.medicine_dose > 0 ? record.medicine_dose + ' มล.' : ''}</p>` : ''}
                      ${record.stool_color ? `<p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.85}px; margin: 4px 0;">🎨 สี: ${record.stool_color} ${record.stool_texture ? '| ลักษณะ: ' + record.stool_texture : ''}</p>` : ''}
                      ${record.notes ? `<p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 8px 0 0 0; padding: 8px; background: ${primaryColor}10; border-radius: 6px;">${record.notes}</p>` : ''}
                    </div>
                  `;
                }).join('')}
              </div>
            `}
          </div>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;

      document.getElementById('health-form').addEventListener('submit', logHealth);
    }

    function renderMilestoneView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      const completedMilestones = milestonesData.filter(m => {
        const record = allData.find(d => d.milestone_id === m.id);
        return record && record.completed;
      });

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 20px;">
          
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <button onclick="currentView='dashboard'; renderApp();" style="padding: 10px 16px; background: ${surfaceColor}; color: ${textColor}; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; cursor: pointer; font-family: 'Sarabun', sans-serif;">← กลับ</button>
            <h2 style="color: ${textColor}; font-size: ${baseSize * 1.5}px; font-weight: 600; margin: 0;">📋 พัฒนาการ</h2>
            <div style="width: 80px;"></div>
          </div>

          <div style="background: linear-gradient(135deg, ${primaryColor}, ${secondaryColor}); padding: 20px; border-radius: 12px; margin-bottom: 20px; text-align: center; color: white;">
            <p style="font-size: ${baseSize * 1.8}px; font-weight: 700; margin: 0 0 8px 0;">${completedMilestones.length} / ${milestonesData.length}</p>
            <p style="font-size: ${baseSize * 1.05}px; margin: 0; opacity: 0.95;">พัฒนาการที่ทำได้แล้ว</p>
          </div>

          <div style="display: flex; flex-direction: column; gap: 12px;">
            ${milestonesData.map(milestone => {
              const record = allData.find(d => d.milestone_id === milestone.id);
              const isCompleted = record && record.completed;
              
              return `
                <div style="background: ${surfaceColor}; padding: 16px; border-radius: 12px; box-shadow: 0 2px 6px rgba(0,0,0,0.08); border-left: 4px solid ${isCompleted ? primaryColor : '#ddd'};">
                  <div style="display: flex; align-items: start; gap: 12px;">
                    <button 
                      onclick="toggleMilestone('${milestone.id}')"
                      style="flex-shrink: 0; width: 28px; height: 28px; border-radius: 50%; border: 2px solid ${primaryColor}; background: ${isCompleted ? primaryColor : 'white'}; cursor: pointer; display: flex; align-items: center; justify-content: center;"
                      ${isLoading ? 'disabled' : ''}
                    >
                      ${isCompleted ? '<span style="color: white; font-size: 16px;">✓</span>' : ''}
                    </button>
                    <div style="flex: 1;">
                      <div style="display: flex; justify-content: space-between; align-items: start; margin-bottom: 4px;">
                        <h3 style="color: ${textColor}; font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0;">${milestone.milestone}</h3>
                        <span style="background: ${secondaryColor}; color: white; padding: 4px 10px; border-radius: 12px; font-size: ${baseSize * 0.75}px; white-space: nowrap; margin-left: 8px;">${milestone.ageRange}</span>
                      </div>
                      <p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.9}px; margin: 4px 0 0 0; background: ${primaryColor}20; padding: 8px; border-radius: 6px;">${milestone.category}</p>
                      <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 8px 0 0 0;">${milestone.description}</p>
                    </div>
                  </div>
                </div>
              `;
            }).join('')}
          </div>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;
    }

    function renderVaccineView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 20px;">
          
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <button onclick="currentView='dashboard'; renderApp();" style="padding: 10px 16px; background: ${surfaceColor}; color: ${textColor}; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; cursor: pointer; font-family: 'Sarabun', sans-serif;">← กลับ</button>
            <h2 style="color: ${textColor}; font-size: ${baseSize * 1.5}px; font-weight: 600; margin: 0;">💉 ตารางวัคซีน</h2>
            <div style="width: 80px;"></div>
          </div>

          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; margin-bottom: 20px;">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 8px 0;">💉 ตารางวัคซีนเด็กไทย</h3>
            <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 0;">ตามคำแนะนำของกรมควบคุมโรค กระทรวงสาธารณสุข</p>
          </div>

          <div style="display: flex; flex-direction: column; gap: 12px;">
            ${vaccinesData.map(vaccine => {
              const record = allData.find(d => d.vaccine_id === vaccine.id);
              const vaccineDate = record?.vaccine_date || '';
              const isCompleted = !!vaccineDate;
              
              return `
                <div style="background: ${surfaceColor}; padding: 16px; border-radius: 12px; box-shadow: 0 2px 6px rgba(0,0,0,0.08); border-left: 4px solid ${isCompleted ? primaryColor : '#ddd'};">
                  <div style="display: flex; justify-content: space-between; align-items: start; margin-bottom: 8px;">
                    <div style="flex: 1;">
                      <div style="display: flex; align-items: center; gap: 8px; margin-bottom: 4px;">
                        <h4 style="color: ${textColor}; font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0;">${vaccine.vaccineName}</h4>
                        ${isCompleted ? '<span style="font-size: 20px;">✅</span>' : ''}
                      </div>
                      <span style="background: ${secondaryColor}; color: white; padding: 4px 10px; border-radius: 12px; font-size: ${baseSize * 0.75}px; display: inline-block;">${vaccine.ageRange}</span>
                    </div>
                  </div>
                  
                  <p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.9}px; margin: 8px 0; background: ${primaryColor}20; padding: 8px; border-radius: 6px;">${vaccine.description}</p>
                  
                  <div style="background: ${secondaryColor}10; padding: 10px; border-radius: 6px; margin-bottom: 10px;">
                    <p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.85}px; margin: 0;"><strong>อาการข้างเคียง:</strong> ${vaccine.sideEffects}</p>
                  </div>
                  
                  <div>
                    <label style="display: block; color: ${textColor}; font-size: ${baseSize * 0.85}px; font-weight: 500; margin-bottom: 4px;">วันที่ฉีด:</label>
                    <input 
                      type="date" 
                      id="vaccine-date-${vaccine.id}"
                      value="${vaccineDate}"
                      onchange="saveVaccineDate('${vaccine.id}', this.value)"
                      style="width: 100%; padding: 8px; border: 1px solid ${primaryColor}; border-radius: 6px; font-size: ${baseSize * 0.85}px; font-family: 'Sarabun', sans-serif; background: white; color: ${textColor};"
                      ${isLoading ? 'disabled' : ''}
                    >
                  </div>
                </div>
              `;
            }).join('')}
          </div>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;
    }

    function renderActivitiesView() {
      const config = window.elementSdk?.config || defaultConfig;
      const bgColor = config.background_color || defaultConfig.background_color;
      const surfaceColor = config.surface_color || defaultConfig.surface_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_action_color || defaultConfig.primary_action_color;
      const secondaryColor = config.secondary_action_color || defaultConfig.secondary_action_color;
      const baseSize = config.font_size || defaultConfig.font_size;

      const ageInfo = childProfile ? calculateAge(childProfile.child_birthdate) : null;
      const ageGroup = ageInfo ? getAgeGroup(ageInfo.months) : "0-3";
      const allActivities = [
        ...activitySuggestions["0-3"],
        ...activitySuggestions["4-6"],
        ...activitySuggestions["7-12"]
      ];
      const currentAgeSuggestions = activitySuggestions[ageGroup] || activitySuggestions["0-3"];

      const app = document.getElementById('app');
      app.innerHTML = `
        <div style="background: ${bgColor}; min-height: 100%; width: 100%; font-family: 'Sarabun', sans-serif; padding: 20px;">
          
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
            <button onclick="currentView='dashboard'; renderApp();" style="padding: 10px 16px; background: ${surfaceColor}; color: ${textColor}; border: 2px solid ${primaryColor}; border-radius: 8px; font-size: ${baseSize * 0.9}px; cursor: pointer; font-family: 'Sarabun', sans-serif;">← กลับ</button>
            <h2 style="color: ${textColor}; font-size: ${baseSize * 1.5}px; font-weight: 600; margin: 0;">🎨 กิจกรรม</h2>
            <div style="width: 80px;"></div>
          </div>

          <div style="background: linear-gradient(135deg, ${primaryColor}20, ${secondaryColor}20); padding: 20px; border-radius: 12px; margin-bottom: 20px; text-align: center; border: 2px solid ${primaryColor}40;">
            <p style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 8px 0;">🎯 เหมาะสำหรับอายุ ${ageInfo ? ageInfo.text : '0-3 เดือน'}</p>
            <p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.9}px; margin: 0;">กิจกรรมเหล่านี้จะช่วยกระตุ้นพัฒนาการ EF (Executive Functions)</p>
          </div>

          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px; margin-bottom: 20px;">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 16px 0;">✨ แนะนำสำหรับวันนี้</h3>
            <div style="display: flex; flex-direction: column; gap: 12px;">
              ${currentAgeSuggestions.map(activity => `
                <div style="background: linear-gradient(135deg, ${primaryColor}10, ${secondaryColor}10); padding: 16px; border-radius: 10px; border-left: 4px solid ${primaryColor};">
                  <div style="display: flex; align-items: start; gap: 12px;">
                    <div style="font-size: 36px;">${activity.icon}</div>
                    <div style="flex: 1;">
                      <h4 style="color: ${textColor}; font-size: ${baseSize * 1.05}px; font-weight: 600; margin: 0 0 6px 0;">${activity.title}</h4>
                      <p style="color: ${textColor}; opacity: 0.8; font-size: ${baseSize * 0.9}px; margin: 0 0 6px 0;">${activity.description}</p>
                      <span style="background: ${primaryColor}; color: white; padding: 4px 10px; border-radius: 12px; font-size: ${baseSize * 0.75}px; display: inline-block;">${activity.duration}</span>
                    </div>
                  </div>
                </div>
              `).join('')}
            </div>
          </div>

          <div style="background: ${surfaceColor}; padding: 20px; border-radius: 12px;">
            <h3 style="color: ${textColor}; font-size: ${baseSize * 1.2}px; font-weight: 600; margin: 0 0 16px 0;">📚 กิจกรรมทั้งหมด (0-12 เดือน)</h3>
            <div style="display: flex; flex-direction: column; gap: 12px;">
              ${allActivities.map(activity => `
                <div style="background: white; padding: 16px; border-radius: 10px; border: 1px solid ${primaryColor}40;">
                  <div style="display: flex; align-items: start; gap: 12px;">
                    <div style="font-size: 32px;">${activity.icon}</div>
                    <div style="flex: 1;">
                      <h4 style="color: ${textColor}; font-size: ${baseSize}px; font-weight: 600; margin: 0 0 4px 0;">${activity.title}</h4>
                      <p style="color: ${textColor}; opacity: 0.7; font-size: ${baseSize * 0.85}px; margin: 0 0 4px 0;">${activity.description}</p>
                      <span style="background: ${secondaryColor}20; color: ${secondaryColor}; padding: 4px 8px; border-radius: 10px; font-size: ${baseSize * 0.7}px; display: inline-block;">${activity.duration}</span>
                    </div>
                  </div>
                </div>
              `).join('')}
            </div>
          </div>

          <div id="toast-message" class="hidden" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: ${textColor}; color: white; padding: 12px 24px; border-radius: 8px; font-size: ${baseSize}px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 1000; transition: opacity 0.3s;"></div>
        </div>
      `;
    }

    initializeApp();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a7a531943048961',t:'MTc2NDY3MzI2OS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
