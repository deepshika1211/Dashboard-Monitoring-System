<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Process Monitoring Dashboard</title>
    <style>
        :root {
            --primary-color: #3498db;
            --secondary-color: #2ecc71;
            --danger-color: #e74c3c;
            --warning-color: #f39c12;
            --dark-color: #2c3e50;
            --light-color: #ecf0f1;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #f5f7fa;
            color: #333;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            padding-bottom: 15px;
            border-bottom: 1px solid #ddd;
        }
        
        h1 {
            color: var(--dark-color);
        }
        
        .status-bar {
            display: flex;
            gap: 15px;
        }
        
        .status-item {
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .status-indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
        }
        
        .online {
            background-color: var(--secondary-color);
        }
        
        .offline {
            background-color: var(--danger-color);
        }
        
        .dashboard {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 20px;
        }
        
        .card {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            padding: 20px;
        }
        
        .card-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .card-title {
            font-size: 18px;
            font-weight: 600;
            color: var(--dark-color);
        }
        
        .chart-container {
            height: 250px;
            position: relative;
        }
        
        .chart-controls {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
            margin-top: 10px;
        }
        
        .chart-btn {
            padding: 5px 10px;
            background-color: var(--light-color);
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
        }
        
        .chart-btn:hover {
            background-color: #ddd;
        }
        
        .process-list {
            grid-column: span 2;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
        }
        
        th, td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid #eee;
        }
        
        th {
            background-color: var(--light-color);
            font-weight: 600;
        }
        
        tr:hover {
            background-color: #f9f9f9;
        }
        
        .badge {
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 12px;
            font-weight: 600;
        }
        
        .running {
            background-color: #d4edda;
            color: #155724;
        }
        
        .sleeping {
            background-color: #fff3cd;
            color: #856404;
        }
        
        .stopped {
            background-color: #f8d7da;
            color: #721c24;
        }
        
        .zombie {
            background-color: #d6d8d9;
            color: #1b1e21;
        }
        
        .action-btn {
            padding: 5px 10px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
            margin-right: 5px;
        }
        
        .kill-btn {
            background-color: var(--danger-color);
            color: white;
        }
        
        .restart-btn {
            background-color: var(--warning-color);
            color: white;
        }
        
        .priority-select {
            padding: 4px;
            border-radius: 4px;
            border: 1px solid #ddd;
        }
        
        .search-bar {
            margin-bottom: 20px;
            display: flex;
            gap: 10px;
        }
        
        .search-input {
            flex: 1;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        
        .filter-select {
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            background-color: white;
        }
        
        .time-range-selector {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .time-range-btn {
            padding: 8px 15px;
            background-color: white;
            border: 1px solid #ddd;
            border-radius: 4px;
            cursor: pointer;
        }
        
        .time-range-btn.active {
            background-color: var(--primary-color);
            color: white;
            border-color: var(--primary-color);
        }
        
        .action-controls {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }
        
        .action-group {
            display: flex;
            gap: 5px;
            align-items: center;
            background-color: var(--light-color);
            padding: 8px 12px;
            border-radius: 4px;
        }
        
        .action-label {
            font-size: 14px;
            font-weight: 600;
        }
        
        .bulk-actions {
            display: flex;
            gap: 10px;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid #eee;
        }
        
        .bulk-btn {
            padding: 8px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-weight: 600;
        }
        
        .bulk-kill {
            background-color: var(--danger-color);
            color: white;
        }
        
        .bulk-restart {
            background-color: var(--warning-color);
            color: white;
        }
        
        .select-all {
            margin-right: 15px;
        }
        
        @media (max-width: 768px) {
            .dashboard {
                grid-template-columns: 1fr;
            }
            
            .process-list {
                grid-column: span 1;
            }
            
            .action-controls {
                flex-direction: column;
            }
            
            .action-group {
                width: 100%;
                justify-content: space-between;
            }
