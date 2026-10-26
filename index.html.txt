<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>iPhone 16 Simulator</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            -webkit-user-select: none;
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "Helvetica Neue", Arial, sans-serif;
        }

        body {
            background-color: #0f1115;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
            color: #fff;
        }

        /* iPhone 16 Container Frame */
        .phone-case {
            width: 380px;
            height: 780px;
            background: linear-gradient(145deg, #2c2d30, #111215);
            border-radius: 54px;
            padding: 12px;
            box-shadow: 
                0 25px 50px -12px rgba(0, 0, 0, 0.7),
                0 0 0 1px rgba(255, 255, 255, 0.1) inset,
                0 0 20px rgba(0, 0, 0, 0.5);
            position: relative;
        }

        /* Physical Side Buttons */
        .btn-volume-up, .btn-volume-down, .btn-action, .btn-power {
            position: absolute;
            background: #2a2b2e;
            border-radius: 3px;
        }
        .btn-action { left: -4px; top: 115px; width: 4px; height: 26px; }
        .btn-volume-up { left: -4px; top: 160px; width: 4px; height: 50px; }
        .btn-volume-down { left: -4px; top: 220px; width: 4px; height: 50px; }
        .btn-power { right: -4px; top: 180px; width: 4px; height: 75px; cursor: pointer; }

        .phone-screen {
            width: 100%;
            height: 100%;
            background-color: #000;
            border-radius: 44px;
            overflow: hidden;
            position: relative;
            background-image: linear-gradient(180deg, #1d263b 0%, #3e505b 35%, #8ab0ab 70%, #e2b49a 100%);
            background-size: cover;
            background-position: center;
        }

        /* Dynamic Island */
        .dynamic-island {
            position: absolute;
            top: 11px;
            left: 50%;
            transform: translateX(-50%);
            width: 110px;
            height: 30px;
            background-color: #000;
            border-radius: 20px;
            z-index: 1000;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 12px;
        }

        /* Status Bar */
        .status-bar {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 48px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 28px 0 28px;
            font-size: 14px;
            font-weight: 600;
            z-index: 999;
            color: #fff;
        }
        .status-right {
            display: flex;
            gap: 6px;
            align-items: center;
            font-size: 12px;
        }

        /* Home Indicator (Bar) */
        .home-indicator {
            position: absolute;
            bottom: 8px;
            left: 50%;
            transform: translateX(-50%);
            width: 130px;
            height: 5px;
            background-color: rgba(255, 255, 255, 0.8);
            border-radius: 3px;
            z-index: 1000;
            cursor: pointer;
        }

        /* Lock Screen */
        .lock-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            pt: 60px;
            z-index: 900;
            transition: transform 0.5s ease-in-out, opacity 0.5s ease-in-out;
            backdrop-filter: blur(0px);
        }

        .lock-screen.unlocked {
            transform: translateY(-100%);
            opacity: 0;
            pointer-events: none;
        }

        .lock-clock-container {
            margin-top: 55px;
            text-align: center;
        }
        .lock-date {
            font-size: 18px;
            font-weight: 600;
            letter-spacing: -0.2px;
            margin-bottom: 2px;
            text-shadow: 0 2px 8px rgba(0,0,0,0.3);
        }
        .lock-time {
            font-size: 76px;
            font-weight: 800;
            letter-spacing: -2px;
            line-height: 1;
            text-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }

        /* Passcode View */
        .passcode-container {
            margin-top: 60px;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
        }

        .passcode-title {
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 16px;
            letter-spacing: -0.1px;
        }

        .passcode-dots {
            display: flex;
            gap: 16px;
            margin-bottom: 30px;
        }
        .dot {
            width: 13px;
            height: 13px;
            border-radius: 50%;
            border: 2px solid #fff;
            transition: background-color 0.2s;
        }
        .dot.filled {
            background-color: #fff;
        }

        .keypad {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 16px 24px;
        }
        .key {
            width: 62px;
            height: 62px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.2);
            backdrop-filter: blur(15px);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            font-size: 24px;
            font-weight: 400;
            cursor: pointer;
            transition: background 0.2s;
        }
        .key:active {
            background: rgba(255, 255, 255, 0.4);
        }
        .key-sub {
            font-size: 9px;
            font-weight: 600;
            letter-spacing: 1px;
            margin-top: -2px;
        }

        .shake {
            animation: shakeAnim 0.4s ease-in-out;
        }
        @keyframes shakeAnim {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-12px); }
            40%, 80% { transform: translateX(12px); }
        }

        /* Home Screen */
        .home-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            padding: 60px 20px 20px 20px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            z-index: 800;
        }

        /* Widget Container */
        .widget-calendar {
            width: 100%;
            height: 140px;
            background: rgba(255, 255, 255, 0.22);
            backdrop-filter: blur(25px);
            -webkit-backdrop-filter: blur(25px);
            border-radius: 22px;
            padding: 14px 16px;
            display: flex;
            gap: 12px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 8px 32px rgba(0,0,0,0.15);
        }
        .cal-left {
            flex: 1.1;
            border-right: 1px solid rgba(255,255,255,0.15);
            padding-right: 10px;
        }
        .cal-month {
            color: #ff3b30;
            font-size: 12px;
            font-weight: 700;
            margin-bottom: 4px;
        }
        .cal-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            
