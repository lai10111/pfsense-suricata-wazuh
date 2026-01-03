pfSense + Suricata + Wazuh 整合實作（期末作業）

一、作業說明
本作業選擇題目二，實作 pfSense 與 Wazuh 的整合，透過在 pfSense 上部署 Suricata 作為入侵偵測系統（IDS），並使用 Wazuh agent 將偵測到的事件傳送至 Wazuh Server 進行集中式分析與監控。
本實驗目標為驗證 pfSense 所產生的安全事件，是否能成功被 Wazuh 接收、解析並顯示於 Dashboard 上。

二、實驗環境
系統架構與 IP 配置
角色	系統	IP 位址
防火牆 / IDS	pfSense + Suricata	192.168.72.2
SIEM Server	Wazuh Server (Ubuntu)	192.168.72.130
攻擊模擬主機	Ubuntu VM (Nmap)	192.168.72.140
三、系統架構說明

本實驗中，所有內網流量皆經由 pfSense 進行轉送。
Suricata 部署於 pfSense 的 LAN 介面上，負責即時監控與偵測可疑網路行為。
當 Suricata 偵測到掃描或異常流量時，會將事件記錄於日誌中，並由 pfSense 上的 Wazuh agent 讀取後傳送至 Wazuh Server。
Wazuh Server 會對事件進行解析與集中顯示，提供統一的安全事件監控介面。

四、攻擊模擬方式

為驗證系統偵測能力，本實驗使用 Nmap 進行 SYN 掃描，模擬常見的網路掃描行為。

sudo nmap -sS 192.168.72.2


此掃描行為會觸發 Suricata 的 Port Scan 相關規則，用以測試 IDS 與 SIEM 的整合效果。

五、實作結果與畫面說明
（一）Nmap 掃描行為

使用 Nmap 從 192.168.72.140 對 pfSense (192.168.72.2) 進行 SYN 掃描，成功模擬掃描攻擊行為。

📸 對應截圖：
![Nmap Scan](screenshots/01_nmap_scan.png)

（二）Suricata 偵測結果（pfSense）

Suricata 成功偵測到多筆 Nmap SYN Scan 行為，顯示來源 IP、目的 IP 及對應 Port，證明 IDS 正常運作。

📸 對應截圖：
![Suricata Alerts](screenshots/02_suricata_alerts.png)

（三）Suricata 服務啟用狀態

Suricata 已成功啟用於 pfSense 的 LAN 介面，並處於 Running 狀態，確認 IDS 為即時運作中。

📸 對應截圖：
![Suricata Running](screenshots/03_suricata_running.png)

（四）Wazuh Security Events（集中分析）

Wazuh Dashboard 成功接收到來自 pfSense 的 Suricata 事件，並解析出掃描行為相關資訊（如 signature、來源與目的 IP），證明 IDS 與 SIEM 整合成功。

📸 對應截圖：
![Wazuh Security Events](screenshots/04_wazuh_security_events.png)

（五）Wazuh Agent 狀態

pfSense 上的 Wazuh agent 顯示為 Active 狀態，代表系統連線正常，事件可持續傳送至 Wazuh Server。

📸 對應截圖：
![Wazuh Agents Active](screenshots/05_wazuh_agents_active.png)


六、結論

本實驗成功完成 pfSense IDS 與 Wazuh SIEM 的整合，並驗證掃描攻擊事件可被即時偵測、記錄與集中分析。
