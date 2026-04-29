# aldr
aldr

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>aldr V23.11</title>
    <style>
        :root { 
            --bg: #1a1a1a; --panel: #111; --accent: #ffffff; --dim: #666; --border: #333; --save: #44aa44; --danger: #ff4444; 
            --today-text: #ffff00; --note-marker: #ff8c00; --percent-color: #00ffff;
        }
        body { font-family: monospace; background: var(--bg); color: #ddd; padding: 0; margin: 0; line-height: 1.2; overflow-x: hidden; display: flex; flex-direction: column; height: 100vh; }
        
        .btn-h { height: 32px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; padding: 0 6px; font-size: 10px; cursor: pointer; flex-shrink: 0; }
        .top-utility-bar { position: fixed; top: 0; width: 100%; z-index: 1000; background: #111; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 4px; padding: 4px 5px; box-sizing: border-box; height: 45px; overflow-x: auto; }
        
        .btn-plus-only { padding: 0 10px !important; font-weight: 900; background: #333; color: #eee; border: 1px solid #555; font-size: 18px; }
        .btn-wide { min-width: 30px; font-size: 14px; padding: 0 !important; }
        .spacer-sm { width: 10px; flex-shrink: 0; }

        .full-modal { position: fixed; top: 45px; left: 0; width: 100%; bottom: 45px; background: rgba(10,10,10,0.98); z-index: 8000; display: none; padding: 20px; box-sizing: border-box; color: #fff; overflow-y: auto; }
        .full-modal.open { display: block; }
        .lock-bg { pointer-events: none; opacity: 0.3; filter: grayscale(1); }