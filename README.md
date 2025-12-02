import 'package:flutter/material.dart';
import 'package:intl/intl.dart'; // ต้องเพิ่ม package intl ใน pubspec.yaml เพื่อจัดการเวลา

void main() {
  runApp(const BabyApp());
}

class BabyApp extends StatelessWidget {
  const BabyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Baby Growth Tracker',
      theme: ThemeData(
        // ใช้โทนสีพาสเทล ดูสบายตาสำหรับพ่อแม่
        primarySwatch: Colors.blue,
        scaffoldBackgroundColor: const Color(0xFFF0F4F8),
        useMaterial3: true,
      ),
      home: const TrackerHomePage(),
    );
  }
}

// โมเดลข้อมูลสำหรับการบันทึกกิจกรรม
class ActivityLog {
  final String activityType; // เช่น กินนม, นอน, ขับถ่าย
  final DateTime timestamp;
  final String note;

  ActivityLog(this.activityType, this.timestamp, this.note);
}

class TrackerHomePage extends StatefulWidget {
  const TrackerHomePage({super.key});

  @override
  State<TrackerHomePage> createState() => _TrackerHomePageState();
}

class _TrackerHomePageState extends State<TrackerHomePage> {
  // รายการบันทึกกิจกรรม (จำลอง Database)
  final List<ActivityLog> _logs = [];

  // ฟังก์ชั่นเพิ่มกิจกรรม
  void _addLog(String type) {
    setState(() {
      _logs.insert(0, ActivityLog(type, DateTime.now(), ''));
    });
    
    // แสดงข้อความแจ้งเตือนด้านล่าง
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('บันทึก $type เรียบร้อยแล้ว!')),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('น้องแสนดี (3 เดือน)', style: TextStyle(fontWeight: FontWeight.bold)),
        backgroundColor: Colors.white,
        elevation: 0,
      ),
      body: Column(
        children: [
          // ส่วนที่ 1: ปุ่มกดด่วน (Quick Actions)
          Padding(
            padding: const EdgeInsets.all(16.0),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.spaceAround,
              children: [
                _buildActionButton(Icons.local_dining, "กินนม", Colors.orange, () => _addLog("กินนม")),
                _buildActionButton(Icons.bedtime, "นอนหลับ", Colors.indigo, () => _addLog("นอนหลับ")),
                _buildActionButton(Icons.baby_changing_station, "ขับถ่าย", Colors.green, () => _addLog("ขับถ่าย")),
              ],
            ),
          ),

          const Divider(),

          // ส่วนที่ 2: ส่วนแสดงประวัติ (History Log)
          Expanded(
            child: _logs.isEmpty 
            ? Center(child: Text("ยังไม่มีกิจกรรมวันนี้", style: TextStyle(color: Colors.grey[400])))
            : ListView.builder(
                itemCount: _logs.length,
                itemBuilder: (context, index) {
                  final log = _logs[index];
                  return Card(
                    margin: const EdgeInsets.symmetric(horizontal: 16, vertical: 4),
                    child: ListTile(
                      leading: CircleAvatar(
                        backgroundColor: _getColorByType(log.activityType).withOpacity(0.2),
                        child: Icon(_getIconByType(log.activityType), color: _getColorByType(log.activityType)),
                      ),
                      title: Text(log.activityType, style: const TextStyle(fontWeight: FontWeight.bold)),
                      subtitle: Text(DateFormat('dd MMM - HH:mm น.').format(log.timestamp)),
                      trailing: const Icon(Icons.more_vert),
                    ),
                  );
                },
              ),
          ),
        ],
      ),
      // ปุ่มเมนูหลักตรงกลาง
      floatingActionButton: FloatingActionButton.extended(
        onPressed: () { /* เปิดหน้าวิเคราะห์กราฟ หรือ AI */ },
        label: const Text('วิเคราะห์ผล'),
        icon: const Icon(Icons.analytics),
        backgroundColor: Colors.pinkAccent,
      ),
    );
  }

  // Widget สร้างปุ่มกดวงกลมสวยๆ
  Widget _buildActionButton(IconData icon, String label, Color color, VoidCallback onTap) {
    return Column(
      children: [
        GestureDetector(
          onTap: onTap,
          child: Container(
            padding: const EdgeInsets.all(20),
            decoration: BoxDecoration(
              color: color.withOpacity(0.1),
              shape: BoxShape.circle,
              border: Border.all(color: color.withOpacity(0.5), width: 2),
            ),
            child: Icon(icon, size: 32, color: color),
          ),
        ),
        const SizedBox(height: 8),
        Text(label, style: TextStyle(fontWeight: FontWeight.bold, color: Colors.grey[700])),
      ],
    );
  }

  // Helper function เลือกสีตามกิจกรรม
  Color _getColorByType(String type) {
    switch (type) {
      case "กินนม": return Colors.orange;
      case "นอนหลับ": return Colors.indigo;
      case "ขับถ่าย": return Colors.green;
      default: return Colors.grey;
    }
  }
  
  // Helper function เลือกไอคอนตามกิจกรรม
  IconData _getIconByType(String type) {
    switch (type) {
      case "กินนม": return Icons.local_dining;
      case "นอนหลับ": return Icons.bedtime;
      case "ขับถ่าย": return Icons.baby_changing_station;
      default: return Icons.circle;
    }
  }
}
