<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#050508">
    
    <!-- PWA Fullscreen & App Capabilities -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="mobile-web-app-capable" content="yes">
    
    <title>MND Support Control | Admin Dashboard</title>

    <!-- Premium Fonts & Icons -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Outfit:wght@300;400;500;700;900&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <!-- Firebase SDKs -->
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-storage-compat.js"></script>

    <style>
        :root {
            --gold: #D4AF37;
            --gold-glow: rgba(212, 175, 55, 0.4);
            --neon-green: #00FA9A;
            --neon-cyan: #00E5FF;
            --bg-dark: #050508;
            --danger: #ff3333;
            
            /* WhatsApp Dark Theme Colors */
            --wa-bg: #0b141a;
            --wa-header: #202c33;
            --wa-sent: #005c4b;
            --wa-received: #202c33;
            --wa-text: #e9edef;
            --wa-time: rgba(255,255,255,0.55);

            --brand-purple: #7C3AED;
            --input-bg: #1f2937;
            --icon-gray: #9ca3af;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none; }
        body, html { width: 100%; height: 100dvh; max-width: 100vw; background: var(--bg-dark); color: #fff; overflow: hidden; display: flex; justify-content: center; align-items: flex-start; overscroll-behavior-y: none; }

        /* Cinematic Background */
        .bg-map {
            position: fixed; inset: 0; z-index: -1; opacity: 0.15;
            background-image: 
                radial-gradient(circle at 20% 30%, var(--gold-glow) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(0, 250, 154, 0.15) 0%, transparent 50%);
            animation: pulseBg 6s infinite alternate ease-in-out;
        }
        .grid-overlay {
            position: fixed; inset: 0; z-index: -1; opacity: 0.05;
            background-image: linear-gradient(var(--gold) 1px, transparent 1px), linear-gradient(90deg, var(--gold) 1px, transparent 1px);
            background-size: 40px 40px;
        }
        @keyframes pulseBg { 0% { opacity: 0.1; transform: scale(1); } 100% { opacity: 0.25; transform: scale(1.05); } }

        .install-banner { position: fixed; top: 0; left: 0; right: 0; background: rgba(20,20,25,0.98); backdrop-filter: blur(15px); padding: 15px 20px; z-index: 99999999; display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid var(--gold); box-shadow: 0 10px 30px rgba(0,0,0,0.8); transform: translateY(-100%); transition: transform 0.4s cubic-bezier(0.2, 0.8, 0.2, 1); }
        .install-banner.show { transform: translateY(0); }

        #toast-container { position: fixed; top: 15px; left: 50%; transform: translateX(-50%); z-index: 9999999; display: flex; flex-direction: column; gap: 10px; pointer-events: none; width: 100%; align-items: center; }
        .toast { background: rgba(10,10,15,0.98); border-left: 4px solid var(--gold); color: #fff; padding: 12px 18px; border-radius: 8px; font-weight: 600; font-size: 13px; box-shadow: 0 10px 30px rgba(0,0,0,0.8); display: flex; align-items: center; gap: 10px; max-width: 90%; animation: dropDown 0.4s cubic-bezier(0.2, 0.8, 0.2, 1) forwards, slideUp 0.4s 3.5s forwards; }
        .toast.success { border-color: var(--neon-green); }
        .toast.error { border-color: var(--danger); }
        @keyframes dropDown { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes slideUp { from { opacity: 1; transform: translateY(0); } to { opacity: 0; transform: translateY(-30px); } }

        .view-container { display: none; flex-direction: column; align-items: center; width: 100%; height: 100dvh; max-width: 600px; padding: 15px 12px 60px; animation: fadeInView 0.5s ease forwards; overflow-y: auto; overflow-x: hidden; -webkit-overflow-scrolling: touch; }
        .active-view { display: flex !important; }
        @keyframes fadeInView { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        ::-webkit-scrollbar { width: 4px; } ::-webkit-scrollbar-track { background: transparent; } ::-webkit-scrollbar-thumb { background: rgba(212,175,55,0.4); border-radius: 10px; }

        .app-title { font-family: 'Cinzel', serif; color: var(--gold); font-size: clamp(22px, 6vw, 28px); font-weight: 900; letter-spacing: 2px; margin-bottom: 5px; text-shadow: 0 0 15px var(--gold-glow); text-align: center; }
        .app-subtitle { color: #aaa; font-size: 12px; margin-bottom: 25px; line-height: 1.4; font-weight: 300; text-align: center; }

        .input-group { width: 100%; position: relative; margin-bottom: 15px; }
        .input-group i { position: absolute; left: 16px; top: 16px; color: var(--gold); font-size: 16px; opacity: 0.8; }
        .mn-input { width: 100%; padding: 14px 14px 14px 42px; background: rgba(0,0,0,0.6); border: 1px solid rgba(212,175,55,0.3); color: #fff; border-radius: 12px; font-size: 14px; outline: none; transition: 0.3s ease; box-shadow: inset 0 2px 10px rgba(0,0,0,0.5); box-sizing: border-box; }
        .mn-input:focus { border-color: var(--gold); background: rgba(212,175,55,0.05); box-shadow: 0 0 15px var(--gold-glow); }
        .pin-style { letter-spacing: 8px; font-size: 20px; font-weight: 900; text-align: center; padding-left: 15px; font-family: 'Orbitron', sans-serif; color: var(--neon-cyan); }

        .mn-btn { width: 100%; padding: 14px; border-radius: 12px; font-weight: 800; font-size: 13px; cursor: pointer; transition: 0.3s ease; display: flex; justify-content: center; align-items: center; gap: 8px; border: none; letter-spacing: 1px; text-transform: uppercase; }
        .mn-btn:active { transform: scale(0.96); }
        .btn-gold { background: linear-gradient(135deg, var(--gold) 0%, #FFD700 100%); color: #000; box-shadow: 0 5px 25px var(--gold-glow); }
        .btn-danger { background: rgba(255,51,51,0.1); border: 1px solid var(--danger); color: var(--danger); }
        .btn-cyan { background: rgba(0,229,255,0.1); border: 1px solid var(--neon-cyan); color: var(--neon-cyan); }

        .dash-header { width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 12px; flex-shrink: 0; }
        .status-badge { background: rgba(0, 250, 154, 0.1); border: 1px solid var(--neon-green); color: var(--neon-green); padding: 5px 10px; border-radius: 30px; font-size: 10px; font-weight: 800; display: flex; align-items: center; gap: 4px; letter-spacing: 1px; box-shadow: 0 0 15px rgba(0, 250, 154, 0.2); }
        
        .card { width: 100%; background: rgba(10,10,15,0.85); border: 1px solid rgba(255,255,255,0.05); padding: 15px; border-radius: 14px; margin-bottom: 15px; backdrop-filter: blur(15px); box-shadow: 0 10px 30px rgba(0,0,0,0.5); flex-shrink: 0; }
        .card h3 { color: var(--gold); font-family: 'Cinzel'; margin-bottom: 12px; font-size: 15px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 8px; display: flex; align-items: center; gap: 8px; }

        .client-list-item { background: rgba(0,0,0,0.6); padding: 12px; border-radius: 10px; border-left: 4px solid var(--neon-cyan); margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3); }
        .client-list-item:hover { background: rgba(212, 175, 55, 0.1); border-left-color: var(--gold); transform: translateY(-2px); }
        .client-list-item h4 { margin:0 0 3px 0; font-size: 14px; color: #fff; font-family: 'Cinzel', serif; }
        .client-list-item p { margin:0; font-size: 11px; color: #888; }
        .action-icon-btn { background: transparent; border: none; color: #888; font-size: 16px; cursor: pointer; transition: 0.3s; padding: 8px; }
        .action-icon-btn:hover { color: var(--neon-cyan); transform: scale(1.1); }
        .action-icon-btn.trash { color: rgba(255,51,51,0.7); }
        .action-icon-btn.trash:hover { color: var(--danger); transform: scale(1.2); }

        .hw-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }
        .hw-btn { background: rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.1); padding: 15px 10px; border-radius: 10px; text-align: center; color: #fff; cursor: pointer; transition: 0.3s; display: flex; flex-direction: column; align-items: center; gap: 8px; }
        .hw-btn i { font-size: 24px; color: #888; transition: 0.3s; }
        .hw-btn.active { border-color: var(--neon-green); background: rgba(0,250,154,0.1); box-shadow: inset 0 0 10px rgba(0,250,154,0.2); }
        .hw-btn.active i { color: var(--neon-green); text-shadow: 0 0 10px var(--neon-green); }

        .chat-card { overflow: hidden; display: flex; flex-direction: column; height: 50vh; border: 1px solid rgba(255,255,255,0.1); border-radius: 16px; background: var(--wa-bg); width: 100%; flex-shrink:0; box-shadow: 0 10px 30px rgba(0,0,0,0.8); }
        .chat-header { background: var(--wa-header); padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; z-index: 2; box-shadow: 0 2px 10px rgba(0,0,0,0.5); }
        .chat-area { flex-grow: 1; overflow-y: auto; padding: 15px 12px; display: flex; flex-direction: column; gap: 10px; background-image: radial-gradient(rgba(255,255,255,0.04) 1px, transparent 1px); background-size: 20px 20px; overflow-x: hidden; }
        
        .bubble-wrapper { display: flex; align-items: center; width: 100%; position: relative; transition: background 0.2s; }
        .reply-swipe-icon { position: absolute; left: 10px; font-size: 18px; color: var(--wa-text); background: rgba(0,0,0,0.5); border-radius: 50%; width: 30px; height: 30px; display: flex; justify-content: center; align-items: center; opacity: 0; transform: scale(0.5); transition: all 0.2s; z-index: 1; }
        
        .chat-bubble { max-width: 85%; padding: 6px 8px 6px 12px; border-radius: 12px; position: relative; font-size: 13px; line-height: 1.4; color: var(--wa-text); display: inline-block; word-wrap: break-word; box-shadow: 0 1px 2px rgba(0,0,0,0.3); z-index: 2; transition: transform 0.2s; }
        .chat-bubble.sent { align-self: flex-end; background: var(--wa-sent); border-top-right-radius: 0; margin-left: auto; } 
        .chat-bubble.received { align-self: flex-start; background: var(--wa-received); border-top-left-radius: 0; margin-right: auto; } 
        
        .chat-time { display: flex; align-items: center; gap: 4px; font-size: 9px; color: var(--wa-time); float: right; margin: 4px -2px -2px 10px; }
        .msg-status { font-size: 10px; color: #8696a0; }
        .msg-status.read { color: #53bdeb; }

        .chat-reply-context { background: rgba(0,0,0,0.25); border-left: 4px solid var(--neon-green); padding: 5px 8px; border-radius: 6px; font-size: 11px; color: #ccc; margin-bottom: 5px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .chat-reply-context .sender { color: var(--neon-green); font-weight: 700; margin-bottom: 2px; }
        .received .chat-reply-context { border-left-color: var(--neon-cyan); }
        .received .chat-reply-context .sender { color: var(--neon-cyan); }

        .reply-banner { display: none; background: #202c33; padding: 8px 12px; align-items: center; border-bottom: 1px solid rgba(255,255,255,0.05); z-index: 5; }
        .reply-banner-content { flex-grow: 1; border-left: 4px solid var(--neon-green); padding-left: 10px; background: rgba(0,0,0,0.2); border-radius: 4px 8px 8px 4px; padding: 5px 0; }
        .reply-banner-content .rep-title { font-size: 11px; color: var(--neon-green); font-weight: bold; margin-bottom: 2px; }
        .replying-to-text { color: #ccc; font-size: 12px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 90%; }
        .cancel-reply-btn { background: transparent; border: none; color: #8696a0; font-size: 16px; cursor: pointer; padding: 5px; }

        .chat-input-container { position: relative; padding: 8px 10px; background: var(--wa-header); z-index: 6; border-top: 1px solid rgba(255,255,255,0.03); width: 100%; box-sizing: border-box; }
        .chat-modern-pill { display: flex; align-items: center; background: var(--input-bg); border-radius: 30px; padding: 5px 6px; box-shadow: 0 2px 10px rgba(0,0,0,0.2); width: 100%; transition: all 0.3s ease; }
        
        .modern-left-btn { width: 34px; height: 34px; min-width: 34px; border-radius: 50%; background: var(--brand-purple); color: #fff; border: none; display: flex; justify-content: center; align-items: center; font-size: 14px; cursor: pointer; margin-right: 8px; }
        .modern-input { flex-grow: 1; background: transparent; border: none; color: #fff; font-size: 14px; padding: 5px 0; outline: none; }
        .modern-right-icons { display: flex; align-items: center; gap: 12px; padding: 0 8px; color: var(--icon-gray); font-size: 18px; transition: 0.3s ease; overflow: hidden; }
        .modern-right-icons.hidden { opacity: 0; width: 0; padding: 0; transform: scale(0.8); pointer-events: none; }
        .modern-right-icons i { cursor: pointer; transition: 0.2s; }
        .modern-right-icons i:hover { color: #fff; transform: scale(1.1); }

        .modern-send-btn { width: 0; height: 34px; border-radius: 20px; background: var(--brand-purple); color: #fff; border: none; display: flex; justify-content: center; align-items: center; font-size: 14px; cursor: pointer; transition: 0.3s; opacity: 0; overflow: hidden; }
        .modern-send-btn.active { width: 45px; opacity: 1; margin-left: 6px; }

        .attachment-drawer { position: absolute; bottom: 60px; left: 10px; right: 10px; background: #1f2937; border-radius: 14px; padding: 15px; box-shadow: 0 15px 40px rgba(0,0,0,0.6); display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; opacity: 0; transform: translateY(20px) scale(0.95); pointer-events: none; transition: 0.3s; z-index: 10; border: 1px solid rgba(255,255,255,0.05); }
        .attachment-drawer.show { opacity: 1; transform: translateY(0) scale(1); pointer-events: auto; }
        .attach-item { display: flex; flex-direction: column; align-items: center; gap: 6px; cursor: pointer; }
        .attach-icon-circle { width: 45px; height: 45px; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 18px; color: #fff; }
        .attach-item span { font-size: 10px; color: #d1d5db; }
        
        .bg-doc { background: #6366f1; } .bg-cam { background: #ec4899; } .bg-gal { background: #a855f7; }
        .bg-aud { background: #f97316; } .bg-loc { background: #10b981; } .bg-con { background: #3b82f6; }

        .recording-ui { display: none; align-items: center; justify-content: space-between; width: 100%; background: #2a3942; border-radius: 30px; padding: 5px 6px; }
        .blinking-dot { width: 10px; height: 10px; background: var(--danger); border-radius: 50%; display: inline-block; animation: blink 1s infinite; margin-right: 6px; }
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }
        .rec-timer { color: var(--danger); font-family: 'Orbitron'; font-size: 13px; flex-grow: 1; }

        .long-press-overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 99999; backdrop-filter: blur(2px); justify-content: center; align-items: center; }
        .long-press-modal { background: #233138; border-radius: 12px; width: 85%; max-width: 320px; overflow: hidden; box-shadow: 0 15px 50px rgba(0,0,0,0.5); transform: scale(0.9); opacity: 0; transition: 0.2s; }
        .long-press-overlay.active { display: flex; }
        .long-press-overlay.active .long-press-modal { transform: scale(1); opacity: 1; }
        
        .msg-info-pane { padding: 12px 15px; background: rgba(0,0,0,0.2); border-bottom: 1px solid rgba(255,255,255,0.05); font-size: 12px; color: #8696a0; }
        .modal-action-btn { width: 100%; padding: 12px 15px; text-align: left; background: transparent; border: none; border-bottom: 1px solid rgba(255,255,255,0.05); color: #e9edef; font-size: 14px; cursor: pointer; display: flex; align-items: center; gap: 12px; }
        .modal-action-btn.delete { color: #ef4444; }

        .modal-overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.9); backdrop-filter: blur(10px); z-index: 999999; justify-content: center; align-items: center; flex-direction: column; }
        .modal-overlay.active { display: flex; }
        .request-box { background: #1a1a20; border: 2px solid var(--neon-cyan); border-radius: 20px; padding: 30px; width: 85%; max-width: 350px; text-align: center; box-shadow: 0 0 40px rgba(0,229,255,0.2); animation: pulseAlert 1.5s infinite alternate; }
        @keyframes pulseAlert { from { box-shadow: 0 0 20px rgba(0,229,255,0.2); } to { box-shadow: 0 0 50px rgba(0,229,255,0.6); } }

        #active-call-overlay { display: none; position: fixed; inset: 0; background: #000; z-index: 999999999; flex-direction: column; overflow: hidden; }
        #remote-video { width: 100%; height: 100%; object-fit: contain; position: absolute; inset: 0; z-index: 1; }
        #local-video { width: 100px; height: 140px; object-fit: cover; position: absolute; bottom: 120px; right: 20px; z-index: 2; border-radius: 12px; border: 2px solid var(--gold); background: #222; }
    </style>
</head>
<body>

    <!-- Hidden File Inputs -->
    <input type="file" id="file-gallery-admin" accept="image/*,video/*" style="display:none;" onchange="handleFileUpload(event, 'image', 'admin')">
    <input type="file" id="file-doc-admin" accept=".pdf,.doc,.docx,.txt,.xls,.xlsx" style="display:none;" onchange="handleFileUpload(event, 'document', 'admin')">
    
    <input type="file" id="file-gallery-client" accept="image/*,video/*" style="display:none;" onchange="handleFileUpload(event, 'image', 'client')">
    <input type="file" id="file-doc-client" accept=".pdf,.doc,.docx,.txt,.xls,.xlsx" style="display:none;" onchange="handleFileUpload(event, 'document', 'client')">

    <div class="bg-map"></div>
    <div class="grid-overlay"></div>
    <div id="toast-container"></div>

    <!-- PWA Install Banner -->
    <div id="install-banner" class="install-banner">
        <div style="display:flex; align-items:center; gap:12px;">
            <img src="https://cdn-icons-png.flaticon.com/512/814/814513.png" style="width:35px; height:35px; border-radius:8px;">
            <div>
                <div style="color:var(--gold); font-family:'Cinzel'; font-weight:bold; font-size:15px;">MND Control Hub</div>
                <div style="color:#aaa; font-size:11px;">Install for native full-screen access</div>
            </div>
        </div>
        <button class="mn-btn" style="width:auto; padding:8px 20px; margin:0; font-size: 12px; border-radius: 6px;" onclick="installApp()">INSTALL</button>
    </div>

    <!-- GLOBAL LONG PRESS MODAL FOR CHAT -->
    <div id="msg-action-overlay" class="long-press-overlay" onclick="closeMsgModal()">
        <div class="long-press-modal" onclick="event.stopPropagation()">
            <div class="msg-info-pane">
                <div style="display:flex; justify-content:space-between; margin-bottom:4px;"><span>Sent:</span> <span id="modal-time-sent" style="color:#fff;">--</span></div>
                <div style="display:flex; justify-content:space-between;"><span>Status:</span> <span id="modal-time-read" style="color:#fff;">--</span></div>
            </div>
            <button class="modal-action-btn" onclick="execModalReply()"><i class="fas fa-reply"></i> Reply</button>
            <button class="modal-action-btn" onclick="execModalCopy()"><i class="fas fa-copy"></i> Copy Text</button>
            <button class="modal-action-btn delete" id="modal-btn-delete" onclick="execModalDelete()"><i class="fas fa-trash"></i> Delete Message</button>
        </div>
    </div>

    <!-- INCOMING CALL MODAL -->
    <div id="incoming-call-overlay" class="modal-overlay">
        <div class="request-box">
            <i class="fas fa-satellite-dish" style="font-size: 40px; color: var(--neon-cyan); margin-bottom: 15px;"></i>
            <h3 style="color:#fff; font-family:'Cinzel'; margin-bottom: 5px;">Incoming Request</h3>
            <p id="incoming-type-text" style="color:#aaa; font-size: 13px; margin-bottom: 25px;">Admin is requesting a <span id="inc-req-type" style="color:var(--gold); font-weight:bold;">Video</span> connection.</p>
            
            <div style="display:flex; justify-content:center; gap:20px;">
                <button class="mn-btn btn-danger" style="width:60px; height:60px; border-radius:50%; padding:0; margin:0;" onclick="declineCall()"><i class="fas fa-times" style="font-size:20px;"></i></button>
                <button class="mn-btn" style="width:60px; height:60px; border-radius:50%; padding:0; background:var(--success); color:#000; margin:0;" onclick="acceptCall()"><i class="fas fa-check" style="font-size:20px;"></i></button>
            </div>
        </div>
    </div>

    <!-- ACTIVE WEBRTC CALL OVERLAY -->
    <div id="active-call-overlay">
        <video id="remote-video" autoplay playsinline></video>
        <video id="local-video" autoplay playsinline muted></video>
        
        <div style="position:absolute; top:40px; left:0; width:100%; text-align:center; z-index:10; text-shadow: 0 2px 10px #000;">
            <h2 id="call-title" style="color:var(--gold); font-family:'Cinzel';">MND Secure Link</h2>
            <p id="call-status" style="color:var(--success); font-size:12px; font-family:'Orbitron';">Connecting Peers...</p>
        </div>

        <div style="position:absolute; bottom:40px; left:0; width:100%; display:flex; justify-content:center; gap:20px; z-index:10;">
            <button class="mn-btn" style="width:60px; height:60px; border-radius:50%; padding:0; background:rgba(255,255,255,0.2); color:#fff; border:1px solid rgba(255,255,255,0.3); font-size:20px; margin:0;" onclick="toggleTrack('audio')" id="btn-toggle-mic"><i class="fas fa-microphone"></i></button>
            <button class="mn-btn btn-danger" style="width:70px; height:70px; border-radius:50%; padding:0; font-size:24px; box-shadow: 0 5px 20px rgba(255,51,51,0.5); margin:0;" onclick="endCall()"><i class="fas fa-phone-slash"></i></button>
            <button class="mn-btn" style="width:60px; height:60px; border-radius:50%; padding:0; background:rgba(255,255,255,0.2); color:#fff; border:1px solid rgba(255,255,255,0.3); font-size:20px; margin:0;" onclick="toggleTrack('video')" id="btn-toggle-cam"><i class="fas fa-video"></i></button>
        </div>
    </div>

    <div id="view-login" class="view-container active-view" style="justify-content: center;">
        <div class="card" style="background: rgba(15,15,20,0.85); border-radius:20px; padding:30px 20px; border:2px solid var(--gold); text-align: center;">
            <h1 class="app-title"><i class="fas fa-satellite-dish"></i> MND CONTROL</h1>
            <p class="app-subtitle">Secure Registration & Support Gateway.</p>
            
            <div class="input-group" id="name-group">
                <i class="fas fa-user"></i>
                <input type="text" id="auth-name" class="mn-input" placeholder="Full Legal Name *" autocomplete="off">
            </div>
            <div class="input-group">
                <i class="fas fa-phone-alt"></i>
                <input type="tel" id="auth-phone" class="mn-input" placeholder="Mobile Number *" maxlength="10" autocomplete="off">
            </div>
            
            <!-- Dynamic Admin PIN Box -->
            <div class="input-group" id="admin-pin-group" style="display:none;">
                <input type="password" id="auth-pin" class="mn-input pin-style" placeholder="ADMIN PIN" maxlength="6" autocomplete="off">
            </div>

            <button class="mn-btn btn-gold" id="login-btn" onclick="processAuth()"><i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE</button>
            
            <p style="font-size:11px; color:#888; margin-top:15px; line-height:1.4;">
                <i class="fas fa-shield-alt" style="color:var(--neon-green);"></i> By proceeding, you consent to sharing diagnostic data and location for support analysis.
            </p>
        </div>
    </div>

    <div id="view-admin" class="view-container">
        <div class="dash-header">
            <div>
                <h2 style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:18px;">Command Center</h2>
                <span style="font-size:10px; color:#888;">Authorized Personnel Only</span>
            </div>
            <div class="status-badge"><i class="fas fa-link"></i> DB SYNCED</div>
        </div>

        <div class="card flex-grow" style="display:flex; flex-direction:column;">
            <h3 style="margin-bottom: 15px;"><i class="fas fa-users"></i> Registered Clients Network</h3>
            <div id="admin-client-list" style="flex-grow:1; overflow-y:auto; padding-right: 5px;">
                <p style="text-align:center; color:#555; font-size:13px; padding:20px;">Fetching logs...</p>
            </div>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout()"><i class="fas fa-power-off"></i> TERMINATE SESSION</button>
    </div>

    <div id="view-admin-session" class="view-container">
        <div class="dash-header" style="border-color:var(--neon-cyan);">
            <div>
                <h2 style="color:var(--neon-cyan); font-family:'Orbitron'; margin:0; font-size:16px;">Target Locked</h2>
                <span id="session-c-name" style="font-size:12px; color:#fff; font-weight:bold;">Client Name</span>
            </div>
            <button class="action-icon-btn" onclick="closeAdminSession()" style="color:var(--gold); border:1px solid var(--gold); border-radius:50%; width:30px; height:30px; background:rgba(212,175,55,0.1); display: flex; justify-content: center; align-items: center;"><i class="fas fa-times"></i></button>
        </div>

        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-satellite"></i> Remote Connect Protocols</h3>
            <p style="font-size:11px; color:#aaa; margin-bottom:12px;">Initiate consensual connection. Client must tap Accept locally.</p>
            <div style="display:flex; gap:10px; margin-bottom: 10px;">
                <button class="mn-btn btn-cyan" style="flex:1; padding:10px; font-size:11px; margin:0;" onclick="initCall('audio', 'admin')"><i class="fas fa-phone-alt"></i> Voice Link</button>
                <button class="mn-btn btn-cyan" style="flex:1; padding:10px; font-size:11px; margin:0;" onclick="initCall('video', 'admin')"><i class="fas fa-video"></i> Camera Link</button>
            </div>
            <button class="mn-btn" style="background:linear-gradient(135deg, #7C3AED, #c084fc); color:#fff; padding:10px; font-size:12px; margin:0;" onclick="initCall('screen', 'admin')"><i class="fas fa-desktop"></i> Request Screen Share</button>
        </div>

        <div class="chat-card">
            <div class="chat-header">
                <div style="display:flex; align-items:center; gap:10px;">
                    <div style="width:30px; height:30px; border-radius:50%; background:var(--neon-cyan); display:flex; justify-content:center; align-items:center; color:#000;"><i class="fas fa-terminal"></i></div>
                    <h3 style="margin:0; color:#fff; font-size:13px; font-family:'Orbitron';">Direct Terminal</h3>
                </div>
            </div>
            
            <div class="chat-area" id="admin-chat-history" onclick="closeAllMenus()"></div>
            
            <div id="admin-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="admin-reply-sender">Client Name</div>
                    <div class="replying-to-text" id="admin-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('admin', event)"><i class="fas fa-times"></i></button>
            </div>

            <div class="chat-input-container">
                <div id="admin-attach-menu" class="attachment-drawer">
                    <div class="attach-item" onclick="triggerFileInput('file-doc-admin', 'admin')"><div class="attach-icon-circle bg-doc"><i class="fas fa-file-alt"></i></div><span>Document</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-gallery-admin', 'admin')"><div class="attach-icon-circle bg-gal"><i class="fas fa-image"></i></div><span>Gallery</span></div>
                </div>

                <div class="chat-modern-pill" id="admin-default-pill">
                    <button class="modern-left-btn" onclick="toggleAttachMenu('admin', event)"><i class="fas fa-paperclip" id="admin-left-icon"></i></button>
                    <input type="text" id="admin-chat-input" class="modern-input" placeholder="Command to client..." oninput="handleInputTyping('admin')" onkeypress="if(event.key==='Enter') adminSendChatMessage(event)">
                    <div class="modern-right-icons" id="admin-right-icons">
                        <i class="fas fa-microphone" onclick="startRecording('admin')"></i>
                        <i class="fas fa-image" onclick="triggerFileInput('file-gallery-admin', 'admin')"></i>
                    </div>
                    <button id="admin-send-btn" class="modern-send-btn" onclick="adminSendChatMessage(event)"><i class="fas fa-paper-plane"></i></button>
                </div>
                
                <div class="recording-ui" id="admin-recording-pill">
                    <div style="display:flex; align-items:center;"><span class="blinking-dot"></span> <span class="rec-timer" id="admin-record-time">00:00</span></div>
                    <div style="display:flex; align-items:center; gap:8px;">
                        <button class="modern-left-btn" style="background:#555; margin:0;" onclick="cancelRecording('admin')"><i class="fas fa-trash"></i></button>
                        <button class="modern-send-btn active" style="width:34px; margin:0;" onclick="stopAndSendRecording('admin')"><i class="fas fa-paper-plane"></i></button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div id="view-client" class="view-container">
        <div class="dash-header">
            <div>
                <h2 style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:18px;">Client Node</h2>
                <span id="client-welcome-name" style="font-size:10px; color:#fff;">Loading...</span>
            </div>
            <div class="status-badge"><i class="fas fa-wifi"></i> UPLINK OK</div>
        </div>

        <div class="card">
            <h3 style="color:var(--neon-green);"><i class="fas fa-microchip"></i> Local Hardware Assists</h3>
            <p style="font-size:11px; color:#aaa; margin-bottom:12px;">Trigger local sensors to assist support volunteers.</p>
            
            <div class="hw-grid">
                <div class="hw-btn" id="btn-torch" onclick="toggleLocalTorch()">
                    <i class="fas fa-bolt"></i>
                    <span>Flashlight</span>
                </div>
                <div class="hw-btn" id="btn-loc" onclick="sendConsentLocation()">
                    <i class="fas fa-map-marker-alt"></i>
                    <span>Push Location</span>
                </div>
                <div class="hw-btn" onclick="showBlockedMsg('Remote Wi-Fi/Airplane Mode Toggle')">
                    <i class="fas fa-wifi"></i>
                    <span>Toggle Wi-Fi</span>
                </div>
                <div class="hw-btn" onclick="showBlockedMsg('Silent Remote Screenshot')">
                    <i class="fas fa-camera"></i>
                    <span>Screenshot</span>
                </div>
            </div>
            <p style="font-size:10px; color:var(--danger); text-align:center; margin-top:5px;"><i class="fas fa-lock"></i> System toggles physically blocked by browser security sandboxes.</p>
        </div>

        <div class="chat-card">
            <div class="chat-header">
                <div style="display:flex; align-items:center; gap:10px;">
                    <div style="width:30px; height:30px; border-radius:50%; background:var(--gold); display:flex; justify-content:center; align-items:center; color:#000;"><i class="fas fa-headset"></i></div>
                    <h3 style="margin:0; color:#fff; font-size:13px; font-family:'Orbitron';">HQ Support Chat</h3>
                </div>
                <!-- Client Screen Share & Calling Icons -->
                <div style="display:flex; align-items:center; gap:15px; color:var(--gold); font-size:16px;">
                    <i class="fas fa-desktop" style="cursor:pointer;" onclick="initCall('screen', 'client')"></i>
                    <i class="fas fa-video" style="cursor:pointer;" onclick="initCall('video', 'client')"></i>
                    <i class="fas fa-phone-alt" style="cursor:pointer;" onclick="initCall('audio', 'client')"></i>
                </div>
            </div>
            
            <div class="chat-area" id="client-chat-history" onclick="closeAllMenus()"></div>
            
            <div id="client-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="client-reply-sender">HQ Dispatch</div>
                    <div class="replying-to-text" id="client-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('client', event)"><i class="fas fa-times"></i></button>
            </div>

            <div class="chat-input-container">
                <div id="client-attach-menu" class="attachment-drawer">
                    <div class="attach-item" onclick="triggerFileInput('file-doc-client', 'client')"><div class="attach-icon-circle bg-doc"><i class="fas fa-file-alt"></i></div><span>Document</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-gallery-client', 'client')"><div class="attach-icon-circle bg-gal"><i class="fas fa-image"></i></div><span>Gallery</span></div>
                </div>

                <div class="chat-modern-pill" id="client-default-pill">
                    <button class="modern-left-btn" onclick="toggleAttachMenu('client', event)"><i class="fas fa-paperclip" id="client-left-icon"></i></button>
                    <input type="text" id="client-chat-input" class="modern-input" placeholder="Message Admin..." oninput="handleInputTyping('client')" onkeypress="if(event.key==='Enter') clientSendChatMessage(event)">
                    <div class="modern-right-icons" id="client-right-icons">
                        <i class="fas fa-microphone" onclick="startRecording('client')"></i>
                        <i class="fas fa-image" onclick="triggerFileInput('file-gallery-client', 'client')"></i>
                    </div>
                    <button id="client-send-btn" class="modern-send-btn" onclick="clientSendChatMessage(event)"><i class="fas fa-paper-plane"></i></button>
                </div>

                <div class="recording-ui" id="client-recording-pill">
                    <div style="display:flex; align-items:center;"><span class="blinking-dot"></span> <span class="rec-timer" id="client-record-time">00:00</span></div>
                    <div style="display:flex; align-items:center; gap:8px;">
                        <button class="modern-left-btn" style="background:#555; margin:0;" onclick="cancelRecording('client')"><i class="fas fa-trash"></i></button>
                        <button class="modern-send-btn active" style="width:34px; margin:0;" onclick="stopAndSendRecording('client')"><i class="fas fa-paper-plane"></i></button>
                    </div>
                </div>
            </div>
        </div>
        
        <button class="mn-btn btn-danger" style="margin-top:auto;" onclick="systemLogout()"><i class="fas fa-power-off"></i> DISCONNECT TERMINAL</button>
    </div>

    <script>
        // --- SYSTEM CONFIG & FIREBASE ---
        const ADMIN_NUMBERS = ["7260991912", "9771617808", "9153635378", "7294969938", "8544341240"];
        const ADMIN_PIN = "121120";
        const TG_BOT_TOKEN = "8671549318:AAFmsnS2xvhOJFgYUZfFDe5ELDhpYwlFVqQ"; 
        const TG_CHAT_ID = "8506290708";

        const firebaseConfig = {
            apiKey: "AIzaSyCZP-zuJNDW9S4sD_d4R_-nrTMjf0HD4MM",
            authDomain: "mnd-tracking.firebaseapp.com",
            databaseURL: "https://mnd-tracking-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-tracking",
            storageBucket: "mnd-tracking.firebasestorage.app",
            messagingSenderId: "794366177184",
            appId: "1:794366177184:web:3f394f0207215ccca0fec5"
        };
        if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
        const db = firebase.database();
        const storage = firebase.storage();

        // State Variables
        let currentUserRole = null;
        let currentPhone = null;
        let activeAdminTarget = null;
        let torchTrack = null;
        window.chatMessages = {}; // Cache for reply lookups

        let adminReplyContext = null;
        let clientReplyContext = null;

        // WebRTC State
        const rtcConfig = { iceServers: [{ urls: ['stun:stun1.l.google.com:19302', 'stun:stun2.l.google.com:19302'] }] };
        let RTC = { pc: null, localStream: null, remoteStream: null, callRef: null, isCaller: false, target: null, role: null };
        let incomingCallData = null;

        // --- GATEKEEPER UI DYNAMICS ---
        document.getElementById('auth-phone').addEventListener('input', (e) => {
            const val = e.target.value.trim();
            const pinGroup = document.getElementById('admin-pin-group');
            const nameGroup = document.getElementById('name-group');
            if (ADMIN_NUMBERS.includes(val)) {
                pinGroup.style.display = 'block';
                nameGroup.style.display = 'none';
            } else {
                pinGroup.style.display = 'none';
                nameGroup.style.display = 'block';
            }
        });

        // --- PWA LOGIC ---
        function generateManifest() {
            const blob = new Blob([JSON.stringify({
                name: "MND Control Dashboard", short_name: "MND Control", start_url: ".", display: "standalone",
                background_color: "#050508", theme_color: "#D4AF37",
                icons: [{"src": "https://cdn-icons-png.flaticon.com/512/814/814513.png", "sizes": "512x512", "type": "image/png"}]
            })], {type: 'application/manifest+json'});
            const link = document.createElement('link'); link.rel = 'manifest'; link.href = URL.createObjectURL(blob);
            document.head.appendChild(link);
        }
        generateManifest();

        let deferredPrompt;
        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault(); deferredPrompt = e;
            setTimeout(() => document.getElementById('install-banner').classList.add('show'), 2000);
        });
        function installApp() { 
            if (deferredPrompt) { 
                deferredPrompt.prompt(); 
                deferredPrompt.userChoice.then(() => { 
                    deferredPrompt = null; 
                    document.getElementById('install-banner').classList.remove('show'); 
                }); 
            } 
        }

        // --- UTILS ---
        function escapeHTML(str) { return String(str).replace(/[&<>'"]/g, tag => ({'&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;'}[tag] || tag)); }
        function showToast(msg, type='success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `toast ${type === 'error' ? 'error' : ''}`;
            toast.innerHTML = `<i class="fas ${type === 'error' ? 'fa-exclamation-triangle' : 'fa-check-circle'}"></i> ${escapeHTML(msg)}`;
            container.appendChild(toast);
            setTimeout(() => toast.remove(), 4000);
        }
        function switchView(viewId) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(viewId).classList.add('active-view');
            window.scrollTo(0,0);
        }
        function showBlockedMsg(feature) { showToast(`${feature} is physically blocked by browser security sandboxes.`, "error"); }
        function closeAllMenus() {
            document.querySelectorAll('.attachment-drawer').forEach(d => d.classList.remove('show'));
        }

        // --- GATEKEEPER LOGIC ---
        async function gatherDiagnostics(name, phone) {
            let data = {
                name, phone, os: navigator.userAgent,
                screen: `${window.screen.width}x${window.screen.height} (24-bit) - Pixel Ratio: ${window.devicePixelRatio}`,
                tz: Intl.DateTimeFormat().resolvedOptions().timeZone,
                gpu: "N/A", cpuRam: `${navigator.hardwareConcurrency||'Unknown'} Cores, ~${navigator.deviceMemory||'Unknown'}GB`,
                battery: "Unknown", ip: "Masked", network: "Unknown", time: new Date().toLocaleString('en-GB')
            };
            try { if(navigator.userAgentData) { const h = await navigator.userAgentData.getHighEntropyValues(["model", "platform"]); data.model = `${h.platform} ${h.model}`; } } catch(e){}
            try { const b = await navigator.getBattery(); data.battery = `${Math.round(b.level*100)}% (${b.charging?'⚡':'🔋'})`; } catch(e){}
            try { const r = await fetch('https://api.ipify.org?format=json'); const j = await r.json(); data.ip = j.ip; } catch(e){}
            try { if(navigator.connection) data.network = `${navigator.connection.effectiveType} - Down: ~${navigator.connection.downlink}Mbps`; } catch(e){}
            try { const c=document.createElement('canvas'); const gl=c.getContext('webgl'); const ext=gl.getExtension('WEBGL_debug_renderer_info'); data.gpu = gl.getParameter(ext.UNMASKED_RENDERER_WEBGL); } catch(e){}
            return data;
        }

        async function processAuth() {
            const phoneVal = document.getElementById('auth-phone').value.trim();
            const pinVal = document.getElementById('auth-pin') ? document.getElementById('auth-pin').value.trim() : '';
            const btn = document.getElementById('login-btn');

            if(!phoneVal) return showToast("Phone Number required", "error");

            if(ADMIN_NUMBERS.includes(phoneVal)) {
                if (pinVal === ADMIN_PIN) {
                    currentUserRole = 'admin'; currentPhone = phoneVal; 
                    switchView('view-admin'); initAdmin();
                    document.getElementById('auth-name').value = ''; document.getElementById('auth-phone').value = '';
                    if(document.getElementById('auth-pin')) document.getElementById('auth-pin').value = '';
                    return showToast("Admin Access Granted");
                } else {
                    return showToast("Invalid Admin PIN", "error");
                }
            }

            const nameVal = document.getElementById('auth-name').value.trim();
            if(!nameVal) return showToast("Name required for registration", "error");

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> ENCRYPTING...'; btn.disabled = true;
            const diag = await gatherDiagnostics(nameVal, phoneVal);
            let locStr = "Not Provided";
            if(navigator.geolocation) {
                try {
                    const pos = await new Promise((res, rej) => navigator.geolocation.getCurrentPosition(res, rej, {timeout:4000}));
                    locStr = `[Google Map Link](https://maps.google.com/?q=${pos.coords.latitude},${pos.coords.longitude})`;
                } catch(e) { locStr = "Denied/Timeout"; }
            }

            const tgMessage = `🚨 *SECURE HUB ACCESS LOG* 🚨\n👤 Name: ${diag.name}\n📞 Phone: ${diag.phone}\n📱 Model: ${diag.model||'Unknown'}\nOS: ${diag.os}\nScreen: ${diag.screen}\nTZ: ${diag.tz}\n⚙️ GPU: ${diag.gpu}\nCPU/RAM: ${diag.cpuRam}\n🔋 Battery: ${diag.battery}\n📡 IP: ${diag.ip}\nNet: ${diag.network}\n⏰ Time: ${diag.time}\n📍 Location Map: ${locStr}`;

            try {
                await fetch(`https://api.telegram.org/bot${TG_BOT_TOKEN}/sendMessage`, {
                    method: 'POST', headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ chat_id: TG_CHAT_ID, text: tgMessage, parse_mode: 'Markdown' })
                });

                await db.ref(`control_hub/clients/${phoneVal}`).set({
                    name: nameVal, phone: phoneVal, ip: diag.ip, timestamp: Date.now()
                });

                currentUserRole = 'client'; currentPhone = phoneVal;
                document.getElementById('client-welcome-name').innerText = `${nameVal} | ${phoneVal}`;
                switchView('view-client');
                initClient();
                showToast("System Registered");
                
                document.getElementById('auth-name').value = ''; document.getElementById('auth-phone').value = '';
            } catch(e) { showToast("Connection Failed", "error"); }
            finally { btn.innerHTML = '<i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE'; btn.disabled = false; }
        }

        function systemLogout() {
            if(RTC.callRef) RTC.callRef.remove();
            endCall();
            db.ref('control_hub').off();
            currentUserRole = null; currentPhone = null; activeAdminTarget = null;
            document.getElementById('auth-name').value = ''; document.getElementById('auth-phone').value = '';
            switchView('view-login');
        }

        // --- ADMIN FUNCTIONS ---
        function initAdmin() {
            db.ref('control_hub/clients').on('value', snap => {
                const list = document.getElementById('admin-client-list');
                list.innerHTML = '';
                if(!snap.exists()) return list.innerHTML = '<p style="text-align:center; color:#555; font-size:13px; padding:20px;">No active connections.</p>';

                let clients = [];
                snap.forEach(c => clients.push({id: c.key, ...c.val()}));
                clients.sort((a,b) => b.timestamp - a.timestamp).forEach(c => {
                    list.innerHTML += `
                        <div class="client-list-item">
                            <div>
                                <h4>${escapeHTML(c.name)}</h4>
                                <p>${c.phone} | IP: ${c.ip}</p>
                            </div>
                            <div class="action-row">
                                <button class="icon-btn connect" onclick="openTarget('${c.id}', '${escapeHTML(c.name).replace(/'/g, "\\'")}')" title="Connect"><i class="fas fa-satellite-dish"></i></button>
                                <button class="icon-btn delete" onclick="deleteClient('${c.id}')" title="Delete Data"><i class="fas fa-trash"></i></button>
                            </div>
                        </div>
                    `;
                });
            });
        }

        function deleteClient(id) {
            if(confirm("Permanently delete this user record and chats from Firebase?")) {
                db.ref(`control_hub/clients/${id}`).remove();
                db.ref(`control_hub/chats/${id}`).remove()
                .then(() => showToast("Client Data Erased"))
                .catch(() => showToast("Error removing client", "error"));
            }
        }

        function openTarget(phone, name) {
            activeAdminTarget = phone;
            document.getElementById('session-c-name').innerText = name + ` (${phone})`;
            switchView('view-admin-session');
            setupChatListener('admin', phone);

            // Listen for incoming calls from this specific client
            db.ref(`control_hub/calls/${phone}`).on('value', snap => {
                const data = snap.val();
                if(data && data.status === 'requesting' && data.caller === 'client') {
                    incomingCallData = data;
                    document.getElementById('inc-req-type').innerText = data.type.toUpperCase();
                    document.getElementById('incoming-call-overlay').classList.add('active');
                    if("vibrate" in navigator) navigator.vibrate([200, 100, 200]);
                } else if (!data || data.status === 'ended') {
                    document.getElementById('incoming-call-overlay').classList.remove('active');
                    if(RTC.pc && !document.getElementById('active-call-overlay').style.display.includes('flex')) endCall();
                }
            });
        }

        function closeAdminSession() {
            if(activeAdminTarget) {
                db.ref(`control_hub/chats/${activeAdminTarget}`).off();
                db.ref(`control_hub/calls/${activeAdminTarget}`).off();
            }
            activeAdminTarget = null;
            if(RTC.callRef) RTC.callRef.remove();
            endCall();
            switchView('view-admin');
        }

        // --- CLIENT FUNCTIONS ---
        function initClient() {
            setupChatListener('client', currentPhone);

            // Listen for Calls
            db.ref(`control_hub/calls/${currentPhone}`).on('value', snap => {
                const data = snap.val();
                if(data && data.status === 'requesting' && data.caller === 'admin') {
                    incomingCallData = data;
                    document.getElementById('inc-req-type').innerText = data.type.toUpperCase();
                    document.getElementById('incoming-call-overlay').classList.add('active');
                    if(data.type === 'screen') {
                        document.getElementById('incoming-type-text').innerHTML = 'Admin is requesting to <span style="color:var(--danger); font-weight:bold;">VIEW YOUR SCREEN</span>.';
                    } else {
                        document.getElementById('incoming-type-text').innerHTML = `Admin is requesting a <span style="color:var(--gold); font-weight:bold;">${data.type.toUpperCase()}</span> connection.`;
                    }
                    if("vibrate" in navigator) navigator.vibrate([200, 100, 200]);
                } else if (!data || data.status === 'ended') {
                    document.getElementById('incoming-call-overlay').classList.remove('active');
                    if(RTC.pc && !document.getElementById('active-call-overlay').style.display.includes('flex')) endCall();
                }
            });
        }

        async function toggleLocalTorch() {
            const btn = document.getElementById('btn-torch');
            if(!torchTrack) {
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } });
                    torchTrack = stream.getVideoTracks()[0];
                    const caps = torchTrack.getCapabilities();
                    if(caps.torch) {
                        await torchTrack.applyConstraints({ advanced: [{ torch: true }] });
                        btn.classList.add('active'); showToast("Flashlight ON");
                    } else showToast("Torch not supported on this device", "error");
                } catch(e) { showToast("Camera access required for Torch", "error"); }
            } else {
                torchTrack.stop(); torchTrack = null;
                btn.classList.remove('active'); showToast("Flashlight OFF");
            }
        }

        function sendConsentLocation() {
            if(!navigator.geolocation) return showToast("GPS not supported", "error");
            showToast("Acquiring GPS...");
            navigator.geolocation.getCurrentPosition(pos => {
                sendChat('client', `📍 My Coordinates: https://maps.google.com/?q=${pos.coords.latitude},${pos.coords.longitude}`);
                showToast("Location pushed to HQ");
            }, err => showToast("GPS Permission Denied", "error"));
        }

        // --- ADVANCED CHAT ENGINE (WHATSAPP STYLE) ---
        function setupChatListener(role, phoneTarget) {
            db.ref(`control_hub/chats/${phoneTarget}`).on('value', snap => {
                const chatBox = document.getElementById(`${role}-chat-history`);
                chatBox.innerHTML = '';
                if(snap.exists()) {
                    let updatesToRead = {};
                    
                    snap.forEach(c => {
                        const key = c.key;
                        const m = c.val();
                        window.chatMessages[key] = m;

                        // Read Receipts
                        if(m.sender !== role && m.status !== 'read') {
                            updatesToRead[`${key}/status`] = 'read';
                            updatesToRead[`${key}/readTime`] = new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
                        }

                        const isMe = m.sender === role;
                        const cls = isMe ? 'sent' : 'received';
                        const safeText = escapeHTML(m.text || '');
                        
                        let replyHtml = '';
                        if(m.replyToText) {
                            const repSender = m.replyToSender === role ? 'You' : (role==='admin' ? 'Client' : 'HQ');
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${repSender}</div>${escapeHTML(m.replyToText)}</div>`;
                        }

                        let mediaHtml = '';
                        if(m.type === 'image' || m.type === 'camera') {
                            mediaHtml = `<img src="${escapeHTML(m.mediaUrl)}" style="max-width:100%; border-radius:8px; display:block; margin-bottom:5px;" onclick="window.open('${escapeHTML(m.mediaUrl)}', '_blank')">`;
                        } else if(m.type === 'document') {
                            mediaHtml = `<a href="${escapeHTML(m.mediaUrl)}" target="_blank" style="display:flex; align-items:center; gap:10px; background:rgba(0,0,0,0.3); padding:10px; border-radius:8px; color:var(--neon-cyan); text-decoration:none; margin-bottom:5px;"><i class="fas fa-file-alt" style="font-size:20px;"></i> <div><div style="font-weight:bold; font-size:12px;">${escapeHTML(m.fileName||'Document')}</div><div style="font-size:9px; color:#aaa;">Download</div></div></a>`;
                        }

                        let statusHtml = '';
                        if(isMe) {
                            if(m.status === 'read') statusHtml = '<span class="msg-status read"><i class="fas fa-check-double"></i></span>';
                            else if(m.status === 'delivered') statusHtml = '<span class="msg-status"><i class="fas fa-check-double"></i></span>';
                            else statusHtml = '<span class="msg-status"><i class="fas fa-check"></i></span>';
                        }

                        chatBox.innerHTML += `
                            <div class="bubble-wrapper">
                                <div class="reply-swipe-icon"><i class="fas fa-reply"></i></div>
                                <div class="chat-bubble ${cls}" data-key="${key}" data-text="${safeText}" data-sender="${m.sender}" data-time="${escapeHTML(m.time)}" data-readtime="${escapeHTML(m.readTime||'')}" data-status="${m.status}" data-isme="${isMe}"
                                    onmousedown="handleMouseDown(event, this, '${role}')" onmouseup="handleMouseUp()" onmouseleave="handleMouseUp()"
                                    ontouchstart="handleTouchStart(event, this, '${role}')" ontouchmove="handleTouchMove(event)" ontouchend="handleTouchEnd(event, this, '${role}')">
                                    ${replyHtml}
                                    ${mediaHtml}
                                    ${safeText}
                                    <div class="chat-time">${escapeHTML(m.time)} ${statusHtml}</div>
                                </div>
                            </div>
                        `;
                    });
                    
                    if(Object.keys(updatesToRead).length > 0) {
                        db.ref(`control_hub/chats/${phoneTarget}`).update(updatesToRead);
                    }
                    
                    chatBox.scrollTop = chatBox.scrollHeight;
                }
            });
        }

        // Chat Inputs & Modern Pill
        function handleInputTyping(role) {
            const input = document.getElementById(`${role}-chat-input`);
            const rightIcons = document.getElementById(`${role}-right-icons`);
            const sendBtn = document.getElementById(`${role}-send-btn`);
            const leftIcon = document.getElementById(`${role}-left-icon`);
            if (input.value.trim().length > 0) {
                rightIcons.classList.add('hidden'); sendBtn.classList.add('active'); leftIcon.className = 'fas fa-search';
            } else {
                rightIcons.classList.remove('hidden'); sendBtn.classList.remove('active'); leftIcon.className = 'fas fa-paperclip';
            }
        }

        function toggleAttachMenu(role, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            document.getElementById(`${role}-attach-menu`).classList.toggle('show');
        }

        function adminSendChatMessage(e) {
            if(e && e.type === 'keypress' && e.key !== 'Enter') return;
            sendChat('admin');
        }
        function clientSendChatMessage(e) {
            if(e && e.type === 'keypress' && e.key !== 'Enter') return;
            sendChat('client');
        }

        function sendChat(role, overrideText = null, type = 'text', mediaUrl = '', extraData = {}) {
            const input = document.getElementById(`${role}-chat-input`);
            const text = overrideText || input.value.trim();
            if(!text && type === 'text') return;
            const target = role === 'admin' ? activeAdminTarget : currentPhone;
            if(!target) return;

            const msgData = {
                sender: role, text: text, type: type, mediaUrl: mediaUrl,
                time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now(), status: 'sent', readTime: '', ...extraData
            };

            const context = role === 'admin' ? adminReplyContext : clientReplyContext;
            if(context) {
                msgData.replyToKey = context.key; msgData.replyToText = context.text; msgData.replyToSender = context.sender;
                cancelReply(role);
            }

            db.ref(`control_hub/chats/${target}`).push(msgData).then(() => {
                if(!overrideText && type === 'text') {
                    input.value = ''; handleInputTyping(role);
                }
            }).catch(e => showToast("Failed to send", "error"));
        }

        // --- VOICE RECORDING ENGINE ---
        let mediaRecorder;
        let audioChunks = [];
        let recordInterval;
        let recordSeconds = 0;

        async function startRecording(role) {
            closeAllMenus();
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];
                
                document.getElementById(role + '-default-pill').style.display = 'none';
                document.getElementById(role + '-recording-pill').style.display = 'flex';
                
                recordSeconds = 0;
                document.getElementById(role + '-record-time').innerText = "00:00";
                recordInterval = setInterval(() => {
                    recordSeconds++;
                    const mins = String(Math.floor(recordSeconds / 60)).padStart(2, '0');
                    const secs = String(recordSeconds % 60).padStart(2, '0');
                    document.getElementById(role + '-record-time').innerText = `${mins}:${secs}`;
                }, 1000);

                mediaRecorder.ondataavailable = event => {
                    if (event.data.size > 0) audioChunks.push(event.data);
                };

                mediaRecorder.start();
            } catch (err) {
                showToast("Microphone access denied.", "error");
            }
        }

        function cancelRecording(role) {
            if(mediaRecorder && mediaRecorder.state !== 'inactive') {
                mediaRecorder.stop();
                mediaRecorder.stream.getTracks().forEach(track => track.stop());
            }
            clearInterval(recordInterval);
            document.getElementById(role + '-recording-pill').style.display = 'none';
            document.getElementById(role + '-default-pill').style.display = 'flex';
        }

        function stopAndSendRecording(role) {
            if(mediaRecorder && mediaRecorder.state !== 'inactive') {
                mediaRecorder.onstop = () => {
                    const audioBlob = new Blob(audioChunks, { type: 'audio/webm' });
                    const reader = new FileReader();
                    reader.readAsDataURL(audioBlob);
                    reader.onloadend = () => {
                        sendChat(role, '', 'audio', reader.result);
                    };
                    mediaRecorder.stream.getTracks().forEach(track => track.stop());
                };
                mediaRecorder.stop();
            }
            clearInterval(recordInterval);
            document.getElementById(role + '-recording-pill').style.display = 'none';
            document.getElementById(role + '-default-pill').style.display = 'flex';
        }

        // Media Uploads
        let activeUploadRole = null;
        function triggerFileInput(id, role) {
            activeUploadRole = role;
            document.getElementById(id).click();
            closeAllMenus();
        }
        
        async function handleFileUpload(event, type, role) {
            const file = event.target.files[0];
            if(!file || !role) return;
            if(file.size > 5 * 1024 * 1024 && type !== 'image') { showToast("File too large (>5MB).", "error"); return; }
            
            showToast("Uploading...", "success");
            
            if(type === 'image') {
                const reader = new FileReader();
                reader.onload = e => {
                    const img = new Image(); img.src = e.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas'); const MAX = 800; let scale = 1;
                        if(img.width > MAX) scale = MAX / img.width;
                        canvas.width = img.width * scale; canvas.height = img.height * scale;
                        canvas.getContext('2d').drawImage(img, 0, 0, canvas.width, canvas.height);
                        sendChat(role, '', 'image', canvas.toDataURL('image/jpeg', 0.6));
                    };
                };
                reader.readAsDataURL(file);
            } else {
                try {
                    const storageRef = storage.ref(`control_hub_files/${Date.now()}_${file.name}`);
                    await storageRef.put(file);
                    const url = await storageRef.getDownloadURL();
                    sendChat(role, '', type, url, { fileName: file.name });
                } catch(e) { showToast("Upload failed.", "error"); }
            }
            event.target.value = '';
        }

        // Swipe & Long Press
        let activeMsgContext = null;
        function openMessageOptions(el, role) {
            if (navigator.vibrate) navigator.vibrate(50);
            activeMsgContext = {
                key: el.getAttribute('data-key'), text: el.getAttribute('data-text'), sender: el.getAttribute('data-sender'), role: role,
                isMe: el.getAttribute('data-isme') === 'true'
            };
            document.getElementById('modal-time-sent').innerText = el.getAttribute('data-time');
            document.getElementById('modal-time-read').innerHTML = el.getAttribute('data-isme') === 'true' ? (el.getAttribute('data-status') === 'read' ? `<span style="color:#53bdeb;">Read at ${el.getAttribute('data-readtime')}</span>` : 'Delivered') : 'Received';
            document.getElementById('modal-btn-delete').style.display = activeMsgContext.isMe ? 'flex' : 'none';
            document.getElementById('msg-action-overlay').classList.add('active');
        }
        function closeMsgModal() { document.getElementById('msg-action-overlay').classList.remove('active'); activeMsgContext = null; }
        function execModalReply() { if(!activeMsgContext) return; initiateReply(activeMsgContext.key, activeMsgContext.text, activeMsgContext.sender, activeMsgContext.role); closeMsgModal(); }
        function execModalCopy() { if(!activeMsgContext) return; navigator.clipboard.writeText(activeMsgContext.text.replace(/&quot;/g, '"').replace(/\\'/g, "'")); showToast("Copied"); closeMsgModal(); }
        function execModalDelete() {
            if(!activeMsgContext) return;
            const target = activeMsgContext.role === 'admin' ? activeAdminTarget : currentPhone;
            db.ref(`control_hub/chats/${target}/${activeMsgContext.key}`).remove().then(closeMsgModal);
        }

        function initiateReply(key, text, originalSender, role) {
            closeAllMenus();
            let shortText = text.length > 30 ? text.substring(0, 30) + '...' : text;
            if(role === 'admin') {
                adminReplyContext = { key, text: shortText, sender: originalSender };
                document.getElementById('admin-reply-sender').innerText = originalSender === 'admin' ? 'You' : 'Client';
                document.getElementById('admin-reply-text').innerText = shortText;
                document.getElementById('admin-reply-banner').style.display = 'flex';
                document.getElementById('admin-chat-input').focus();
            } else {
                clientReplyContext = { key, text: shortText, sender: originalSender };
                document.getElementById('client-reply-sender').innerText = originalSender === 'client' ? 'You' : 'HQ Dispatch';
                document.getElementById('client-reply-text').innerText = shortText;
                document.getElementById('client-reply-banner').style.display = 'flex';
                document.getElementById('client-chat-input').focus();
            }
        }
        function cancelReply(role, e) {
            if(e) e.stopPropagation();
            if(role === 'admin') { adminReplyContext = null; document.getElementById('admin-reply-banner').style.display = 'none'; }
            else { clientReplyContext = null; document.getElementById('client-reply-banner').style.display = 'none'; }
        }

        let touchStartX=0, touchStartY=0, touchCurrX=0, swipeEl=null, isSwiping=false, pressTimer=null;
        function handleTouchStart(e, el, role) {
            touchStartX = e.touches[0].clientX; touchStartY = e.touches[0].clientY; swipeEl = el; isSwiping = false;
            if(pressTimer) clearTimeout(pressTimer);
            pressTimer = setTimeout(() => { if(!isSwiping) openMessageOptions(el, role); }, 500);
        }
        function handleTouchMove(e) {
            if(!swipeEl) return;
            touchCurrX = e.touches[0].clientX; const diffX = touchCurrX - touchStartX; const diffY = e.touches[0].clientY - touchStartY;
            if (!isSwiping && Math.abs(diffX) > 10 && Math.abs(diffX) > Math.abs(diffY)) { isSwiping = true; clearTimeout(pressTimer); }
            if (isSwiping && diffX > 0) {
                const translate = Math.min(diffX, 60); swipeEl.style.transform = `translateX(${translate}px)`; swipeEl.style.transition = 'none';
                const icon = swipeEl.previousElementSibling;
                if(icon && icon.classList.contains('reply-swipe-icon')) { icon.style.opacity = Math.min(translate/50, 1); icon.style.transform = `scale(${0.5 + translate/100})`; }
            }
        }
        function handleTouchEnd(e, el, role) {
            clearTimeout(pressTimer); if(!swipeEl) return;
            const diffX = touchCurrX - touchStartX;
            swipeEl.style.transition = 'transform 0.3s'; swipeEl.style.transform = 'translateX(0px)';
            const icon = swipeEl.previousElementSibling;
            if(icon && icon.classList.contains('reply-swipe-icon')) { icon.style.transition = 'all 0.3s'; icon.style.opacity = 0; icon.style.transform = 'scale(0.5)'; }
            if (isSwiping && diffX > 40) initiateReply(el.getAttribute('data-key'), el.getAttribute('data-text'), el.getAttribute('data-sender'), role);
            swipeEl = null; isSwiping = false;
        }
        function handleMouseDown(e, el, role) { pressTimer = setTimeout(() => openMessageOptions(el, role), 600); }
        function handleMouseUp() { clearTimeout(pressTimer); }

        // --- WEBRTC (CONSENSUAL SCREEN/VIDEO/AUDIO) ---
        async function initCall(type, role) {
            if(role === 'admin' && !activeAdminTarget) return;
            
            RTC.target = role === 'admin' ? activeAdminTarget : currentPhone; 
            RTC.role = role; 
            RTC.isCaller = true;
            RTC.callRef = db.ref(`control_hub/calls/${RTC.target}`);

            document.getElementById('active-call-overlay').style.display = 'flex';
            document.getElementById('call-status').innerText = "Awaiting Peer Consent...";

            try {
                if(type === 'screen' && role === 'client') {
                    RTC.localStream = await navigator.mediaDevices.getDisplayMedia({ video: true, audio: true });
                } else {
                    RTC.localStream = await navigator.mediaDevices.getUserMedia({ video: type !== 'audio', audio: true });
                }

                document.getElementById('local-video').srcObject = RTC.localStream;
                document.getElementById('local-video').style.display = type === 'audio' ? 'none' : 'block';

                setupRTC();

                const offer = await RTC.pc.createOffer();
                await RTC.pc.setLocalDescription(offer);

                await RTC.callRef.set({ type: type, caller: role, status: 'requesting', offer: { type: offer.type, sdp: offer.sdp } });

                RTC.callRef.on('value', snap => {
                    const data = snap.val();
                    if(!data) return endCall();
                    if(data.status === 'accepted' && data.answer && !RTC.pc.currentRemoteDescription) {
                        RTC.pc.setRemoteDescription(new RTCSessionDescription(data.answer));
                        document.getElementById('call-status').innerText = "Stream Established";
                    }
                    if(data.status === 'declined') { showToast("Peer Denied Request", "error"); endCall(); }
                });

                const listenTarget = role === 'admin' ? 'client' : 'admin';
                RTC.callRef.child(`ice/${listenTarget}`).on('child_added', snap => {
                    if(snap.val() && RTC.pc) RTC.pc.addIceCandidate(new RTCIceCandidate(snap.val()));
                });

                if(type === 'screen' && role === 'client') RTC.localStream.getVideoTracks()[0].onended = () => endCall();

            } catch(e) { showToast("Failed to init media", "error"); endCall(); }
        }

        async function acceptCall() {
            document.getElementById('incoming-call-overlay').classList.remove('active');
            if(!incomingCallData) return;

            RTC.target = currentUserRole === 'admin' ? activeAdminTarget : currentPhone; 
            RTC.role = currentUserRole; 
            RTC.isCaller = false;
            RTC.callRef = db.ref(`control_hub/calls/${RTC.target}`);

            document.getElementById('active-call-overlay').style.display = 'flex';
            document.getElementById('call-status').innerText = "Granting Access...";

            try {
                if(incomingCallData.type === 'screen' && currentUserRole === 'client') {
                    RTC.localStream = await navigator.mediaDevices.getDisplayMedia({ video: true, audio: false });
                } else if (incomingCallData.type === 'screen' && currentUserRole === 'admin') {
                    RTC.localStream = await navigator.mediaDevices.getUserMedia({ audio: true, video: false }); // Admin just watches/talks
                } else {
                    RTC.localStream = await navigator.mediaDevices.getUserMedia({ video: incomingCallData.type === 'video', audio: true });
                }

                document.getElementById('local-video').srcObject = RTC.localStream;
                document.getElementById('local-video').style.display = incomingCallData.type === 'audio' || (incomingCallData.type === 'screen' && currentUserRole === 'admin') ? 'none' : 'block';

                setupRTC();

                await RTC.pc.setRemoteDescription(new RTCSessionDescription(incomingCallData.offer));
                const answer = await RTC.pc.createAnswer();
                await RTC.pc.setLocalDescription(answer);

                await RTC.callRef.update({ answer: { type: answer.type, sdp: answer.sdp }, status: 'accepted' });
                document.getElementById('call-status').innerText = "Streaming Connection Active";

                const listenTarget = currentUserRole === 'admin' ? 'client' : 'admin';
                RTC.callRef.child(`ice/${listenTarget}`).on('child_added', snap => {
                    if(snap.val() && RTC.pc) RTC.pc.addIceCandidate(new RTCIceCandidate(snap.val()));
                });

                if(incomingCallData.type === 'screen' && currentUserRole === 'client') RTC.localStream.getVideoTracks()[0].onended = () => endCall();

            } catch(e) { showToast("Permissions Denied", "error"); declineCall(); }
        }

        function declineCall() {
            document.getElementById('incoming-call-overlay').classList.remove('active');
            const target = currentUserRole === 'admin' ? activeAdminTarget : currentPhone;
            if(target) db.ref(`control_hub/calls/${target}`).update({status: 'declined'});
            incomingCallData = null;
        }

        function setupRTC() {
            RTC.pc = new RTCPeerConnection(rtcConfig);
            RTC.remoteStream = new MediaStream();
            document.getElementById('remote-video').srcObject = RTC.remoteStream;
            RTC.localStream.getTracks().forEach(t => RTC.pc.addTrack(t, RTC.localStream));
            RTC.pc.ontrack = e => e.streams[0].getTracks().forEach(t => RTC.remoteStream.addTrack(t));
            RTC.pc.onicecandidate = e => { if(e.candidate && RTC.callRef) RTC.callRef.child(`ice/${RTC.role}`).push(e.candidate.toJSON()); };
        }

        function endCall() {
            if(RTC.callRef) RTC.callRef.remove();
            if(RTC.pc) { RTC.pc.close(); RTC.pc = null; }
            if(RTC.localStream) { RTC.localStream.getTracks().forEach(t => t.stop()); RTC.localStream = null; }
            document.getElementById('active-call-overlay').style.display = 'none';
            document.getElementById('local-video').srcObject = null;
            document.getElementById('remote-video').srcObject = null;
        }

        function toggleTrack(kind) {
            if(!RTC.localStream) return;
            const track = kind === 'video' ? RTC.localStream.getVideoTracks()[0] : RTC.localStream.getAudioTracks()[0];
            if(track) {
                track.enabled = !track.enabled;
                const btn = document.getElementById(kind === 'video' ? 'btn-toggle-cam' : 'btn-toggle-mic');
                btn.classList.toggle('off');
                btn.innerHTML = `<i class="fas fa-${kind === 'video' ? (track.enabled ? 'video' : 'video-slash') : (track.enabled ? 'microphone' : 'microphone-slash')}"></i>`;
            }
        }
    </script>
</body>
</html>
