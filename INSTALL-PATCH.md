# แพตช์ปุ่มติดตั้งแอป (แก้ index.html 3 จุด)

## 1) ใน viewHome() — วางบรรทัด ${installCard()} ต่อจากแบนเนอร์ "New month started"
(ก่อน <section class="${card} mb-4"> ที่แสดง Month balance)

    ${state.justReset ? `...` : ''}
    ${installCard()}
    <section class="${card} mb-4">

## 2) ใน app.addEventListener('click', ...) — เพิ่มบรรทัดนี้ต่อจาก if (t.id === 'newEntry') ...

    if (t.id === 'installApp') return installApp();

## 3) วางบล็อกนี้ก่อนบรรทัด "/* ============ Boot ============ */"

    /* ============ Install (PWA) ============ */
    let deferredPrompt = null;
    const isStandalone = () => matchMedia('(display-mode: standalone)').matches || navigator.standalone === true;
    const isIOS = /iphone|ipad|ipod/i.test(navigator.userAgent) || (navigator.platform === 'MacIntel' && navigator.maxTouchPoints > 1);

    window.addEventListener('beforeinstallprompt', e => {
        e.preventDefault(); deferredPrompt = e;
        if (!state.step && state.tab === 'track') render();
    });
    window.addEventListener('appinstalled', () => { deferredPrompt = null; render(); });

    function installCard() {
        if (isStandalone()) return '';
        return `
    <section class="${card} mb-4 flex items-center gap-3">
      <img src="icon.svg" alt="" class="h-14 w-14 rounded-2xl shadow shrink-0">
      <div class="flex-1 min-w-0">
        <p class="font-semibold leading-tight">ติดตั้งแอป DET</p>
        <p class="text-xs text-slate-500">เปิดใช้งานจากหน้าจอหลักได้ทันที ใช้ได้แม้ไม่มีเน็ต</p>
      </div>
      <button id="installApp" class="shrink-0 rounded-xl bg-emerald-500 hover:bg-emerald-600 text-white text-sm font-semibold px-4 py-2.5 active:scale-[.98] transition">ติดตั้ง</button>
    </section>`;
    }

    async function installApp() {
        if (deferredPrompt) {
            deferredPrompt.prompt();
            await deferredPrompt.userChoice;
            deferredPrompt = null; render(); return;
        }
        if (isIOS) {
            alert('วิธีติดตั้งบน iPhone/iPad:\n1) เปิดหน้านี้ใน Safari\n2) กดปุ่มแชร์ (ไอคอนสี่เหลี่ยมมีลูกศรขึ้น)\n3) เลือก "เพิ่มไปยังหน้าจอโฮม" แล้วกด "เพิ่ม"');
        } else {
            alert('วิธีติดตั้ง:\nเปิดเมนูของเบราว์เซอร์ (⋮) แล้วเลือก "ติดตั้งแอป" หรือ "เพิ่มลงในหน้าจอหลัก"\n\n(ต้องเปิดผ่าน https หรือ localhost ปุ่มนี้ถึงติดตั้งอัตโนมัติได้)');
        }
    }
