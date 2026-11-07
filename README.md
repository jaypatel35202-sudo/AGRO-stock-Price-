# AGRO-stock-Price-
import 'package:flutter/material.dart';

void main() {
  runApp(const AgroApp());
}

class AgroApp extends StatelessWidget {
  const AgroApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'એગ્રો સ્ટોક & ભાવ',
      theme: ThemeData(
        useMaterial3: true,
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.green),
      ),
      home: const PhoneLoginScreen(),
    );
  }
}

// ---------------------------------------------------------------------------
// સ્ક્રીન ૧: મોબાઇલ નંબર અને OTP લોગિન સ્ક્રીન
// ---------------------------------------------------------------------------
class PhoneLoginScreen extends StatefulWidget {
  const PhoneLoginScreen({super.key});

  @override
  State<PhoneLoginScreen> createState() => _PhoneLoginScreenState();
}

class _PhoneLoginScreenState extends State<PhoneLoginScreen> {
  final TextEditingController _phoneController = TextEditingController();
  final TextEditingController _otpController = TextEditingController();

  bool _isOtpSent = false;
  bool _isLoading = false;

  // ૧. OTP મોકલવાનું ફંક્શન
  void _sendOtp() {
    String phone = _phoneController.text.trim();
    if (phone.length != 10) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('કૃપા કરીને સાચો ૧૦ આંકડાનો મોબાઈલ નંબર નાખો!')),
      );
      return;
    }

    setState(() => _isLoading = true);

    // અહીં Firebase Phone Auth નો કોડ લાગશે (ડેમો માટે ૨ સેકન્ડનો વિલંબ આપેલ છે)
    Future.delayed(const Duration(seconds: 2), () {
      setState(() {
        _isLoading = false;
        _isOtpSent = true;
      });
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('+91 $phone પર OTP મોકલવામાં આવ્યો છે (ટેસ્ટ OTP: 123456)')),
      );
    });
  }

  // ૨. OTP વેરિફાય કરીને લોગિન કરવાનું ફંક્શન
  void _verifyOtp() {
    String otp = _otpController.text.trim();
    if (otp.isEmpty || otp.length < 4) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('કૃપા કરીને પૂરો OTP નાખો!')),
      );
      return;
    }

    setState(() => _isLoading = true);

    // OTP વેરિફિકેશન
    Future.delayed(const Duration(seconds: 1), () {
      setState(() => _isLoading = false);
      // સફળ લોગિન પછી મુખ્ય સ્ટોક સ્ક્રીન પર જાઓ
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (context) => AgroHomeScreen(userPhone: _phoneController.text)),
      );
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      body: Center(
        child: SingleChildScrollView(
          padding: const EdgeInsets.all(24.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              // લોગો / આઈકોન
              Container(
                padding: const EdgeInsets.all(20),
                decoration: BoxDecoration(
                  color: Colors.green.shade50,
                  shape: BoxShape.circle,
                ),
                child: Icon(Icons.agriculture, size: 70, color: Colors.green.shade800),
              ),
              const SizedBox(height: 20),
              const Text(
                'એગ્રો સ્ટોર લોગિન',
                style: TextStyle(fontSize: 26, fontWeight: FontWeight.bold, color: Colors.black87),
              ),
              const SizedBox(height: 8),
              const Text(
                'કોઈપણ મોબાઈલમાં આ નંબરથી લોગિન કરી શકો છો',
                textAlign: TextAlign.center,
                style: TextStyle(fontSize: 14, color: Colors.black54),
              ),
              const SizedBox(height: 35),

              // જો OTP હજુ મોકલ્યો ન હોય તો: મોબાઈલ નંબરનું બોક્સ
              if (!_isOtpSent) ...[
                TextField(
                  controller: _phoneController,
                  keyboardType: TextInputType.phone,
                  maxLength: 10,
                  decoration: InputDecoration(
                    prefixIcon: const Icon(Icons.phone),
                    prefixText: '+91 ',
                    prefixStyle: const TextStyle(fontWeight: FontWeight.bold, fontSize: 16),
                    labelText: 'મોબાઇલ નંબર નાખો',
                    border: OutlineInputBorder(borderRadius: BorderRadius.circular(12)),
                  ),
                ),
                const SizedBox(height: 15),
                SizedBox(
                  width: double.infinity,
                  height: 50,
                  child: ElevatedButton(
                    style: ElevatedButton.styleFrom(
                      backgroundColor: Colors.green.shade700,
                      foregroundColor: Colors.white,
                      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
                    ),
                    onPressed: _isLoading ? null : _sendOtp,
                    child: _isLoading
                        ? const CircularProgressIndicator(color: Colors.white)
                        : const Text('OTP મેળવો (Send OTP)', style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
                  ),
                ),
              ]

              // જો OTP મોકલાઈ ગયો હોય તો: OTP નું બોક્સ
              else ...[
                Text(
                  '+91 ${_phoneController.text} પર મોકલેલો OTP નાખો',
                  style: const TextStyle(fontWeight: FontWeight.w600, color: Colors.black87),
                ),
                const SizedBox(height: 15),
                TextField(
                  controller: _otpController,
                  keyboardType: TextInputType.number,
                  maxLength: 6,
                  textAlign: TextAlign.center,
                  style: const TextStyle(fontSize: 22, letterSpacing: 8, fontWeight: FontWeight.bold),
                  decoration: InputDecoration(
                    hintText: '------',
                    border: OutlineInputBorder(borderRadius: BorderRadius.circular(12)),
                  ),
                ),
                const SizedBox(height: 15),
                SizedBox(
                  width: double.infinity,
                  height: 50,
                  child: ElevatedButton(
                    style: ElevatedButton.styleFrom(
                      backgroundColor: Colors.green.shade700,
                      foregroundColor: Colors.white,
                      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
                    ),
                    onPressed: _isLoading ? null : _verifyOtp,
                    child: _isLoading
                        ? const CircularProgressIndicator(color: Colors.white)
                        : const Text('વેરીફાય અને લોગિન કરો', style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
                  ),
                ),
                TextButton(
                  onPressed: () => setState(() => _isOtpSent = false),
                  child: const Text('નંબર બદલવો છે? પાછા જાઓ'),
                ),
              ],
            ],
          ),
        ),
      ),
    );
  }
}

// ---------------------------------------------------------------------------
// સ્ક્રીન ૨: મુખ્ય એગ્રો સ્ટોક સ્ક્રીન (Logout બટન સાથે)
// ---------------------------------------------------------------------------
class AgroItem {
  String id;
  String name;
  String category;
  String weight;
  double price;
  int stock;
  String unit;

  AgroItem({
    required this.id,
    required this.name,
    required this.category,
    required this.weight,
    required this.price,
    required this.stock,
    required this.unit,
  });
}

class AgroHomeScreen extends StatefulWidget {
  final String userPhone;
  const AgroHomeScreen({super.key, required this.userPhone});

  @override
  State<AgroHomeScreen> createState() => _AgroHomeScreenState();
}

class _AgroHomeScreenState extends State<AgroHomeScreen> {
  List<AgroItem> items = [
    AgroItem(id: '1', name: 'DAP ખાતર', category: 'ખાતર', weight: '50 kg', price: 1350.0, stock: 50, unit: 'બેગ'),
    AgroItem(id: '2', name: 'યુરિયા (Urea)', category: 'ખાતર', weight: '45 kg', price: 267.0, stock: 120, unit: 'બેગ'),
    AgroItem(id: '3', name: 'કપાસ બિયારણ (Bolgard)', category: 'બિયારણ', weight: '475 gm', price: 860.0, stock: 35, unit: 'પેકેટ'),
    AgroItem(id: '4', name: 'મગફળી બિયારણ (G-20)', category: 'બિયારણ', weight: '30 kg', price: 3750.0, stock: 40, unit: 'બેગ'),
    AgroItem(id: '5', name: 'કોરાજન (Coragen)', category: 'દવા', weight: '150 ml', price: 1750.0, stock: 15, unit: 'બોટલ'),
  ];

  String selectedCategory = 'બધા';
  String searchQuery = '';

  List<AgroItem> get filteredItems {
    return items.where((item) {
      final matchesCategory = (selectedCategory == 'બધા') || (item.category == selectedCategory);
      final matchesSearch = item.name.toLowerCase().contains(searchQuery.toLowerCase()) ||
                            item.weight.toLowerCase().contains(searchQuery.toLowerCase());
      return matchesCategory && matchesSearch;
    }).toList();
  }

  // નામ અને વજન સુધારવા
  void _editProductDetailsDialog(AgroItem item) {
    TextEditingController nameController = TextEditingController(text: item.name);
    TextEditingController weightController = TextEditingController(text: item.weight);
    String selectedCat = item.category;

    showDialog(
      context: context,
      builder: (context) => StatefulBuilder(
        builder: (context, setModalState) => AlertDialog(
          title: const Text('પ્રોડક્ટની વિગત સુધારો'),
          content: SingleChildScrollView(
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                TextField(
                  controller: nameController,
                  decoration: const InputDecoration(labelText: 'પ્રોડક્ટનું નામ', border: OutlineInputBorder()),
                ),
                const SizedBox(height: 12),
                TextField(
                  controller: weightController,
                  decoration: const InputDecoration(labelText: 'વજન / પેકિંગ (દા.ત. 50 kg)', border: OutlineInputBorder()),
                ),
                const SizedBox(height: 12),
                DropdownButtonFormField<String>(
                  value: selectedCat,
                  decoration: const InputDecoration(labelText: 'કેટેગરી', border: OutlineInputBorder()),
                  items: ['ખાતર', 'બિયારણ', 'દવા'].map((c) => DropdownMenuItem(value: c, child: Text(c))).toList(),
                  onChanged: (val) => setModalState(() => selectedCat = val!),
                ),
              ],
            ),
          ),
          actions: [
            TextButton(onPressed: () => Navigator.pop(context), child: const Text('રદ કરો')),
            ElevatedButton(
              onPressed: () {
                if (nameController.text.isNotEmpty && weightController.text.isNotEmpty) {
                  setState(() {
                    item.name = nameController.text;
                    item.weight = weightController.text;
                    item.category = selectedCat;
                  });
                  Navigator.pop(context);
                }
              },
              child: const Text('સેવ કરો'),
            ),
          ],
        ),
      ),
    );
  }

  // ભાવ બદલવા
  void _editPriceDialog(AgroItem item) {
    TextEditingController priceController = TextEditingController(text: item.price.toString());
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('${item.name} નો ભાવ બદલો'),
        content: TextField(
          controller: priceController,
          keyboardType: const TextInputType.numberWithOptions(decimal: true),
          decoration: const InputDecoration(labelText: 'નવો વેચાણ ભાવ (₹)', border: OutlineInputBorder()),
        ),
        actions: [
          TextButton(onPressed: () => Navigator.pop(context), child: const Text('રદ કરો')),
          ElevatedButton(
            onPressed: () {
              double? newPrice = double.tryParse(priceController.text);
              if (newPrice != null) {
                setState(() => item.price = newPrice);
                Navigator.pop(context);
              }
            },
            child: const Text('સેવ કરો'),
          ),
        ],
      ),
    );
  }

  // સ્ટોક આવક/જાવક
  void _updateStockDialog(AgroItem item) {
    TextEditingController inController = TextEditingController(text: '0');
    TextEditingController outController = TextEditingController(text: '0');

    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('${item.name} (${item.weight})'),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('હાલનો સ્ટોક: ${item.stock} ${item.unit}', style: const TextStyle(fontWeight: FontWeight.bold)),
            const SizedBox(height: 12),
            TextField(controller: inController, keyboardType: TextInputType.number, decoration: const InputDecoration(labelText: '+ આવક (Stock In)', border: OutlineInputBorder())),
            const SizedBox(height: 10),
            TextField(controller: outController, keyboardType: TextInputType.number, decoration: const InputDecoration(labelText: '- જાવક (Stock Out)', border: OutlineInputBorder())),
          ],
        ),
        actions: [
          TextButton(onPressed: () => Navigator.pop(context), child: const Text('રદ કરો')),
          ElevatedButton(
            onPressed: () {
              int stockIn = int.tryParse(inController.text) ?? 0;
              int stockOut = int.tryParse(outController.text) ?? 0;
              setState(() => item.stock = item.stock + stockIn - stockOut);
              Navigator.pop(context);
            },
            child: const Text('સેવ કરો'),
          ),
        ],
      ),
    );
  }

  // લોગઆઉટ કરવા માટે
  void _logout() {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('લોગઆઉટ'),
        content: const Text('શું તમે ખરેખર લોગઆઉટ કરવા માંગો છો?'),
        actions: [
          TextButton(onPressed: () => Navigator.pop(context), child: const Text('ના')),
          ElevatedButton(
            onPressed: () {
              Navigator.pop(context);
              Navigator.pushReplacement(
                context,
                MaterialPageRoute(builder: (context) => const PhoneLoginScreen()),
              );
            },
            child: const Text('હા, લોગઆઉટ કરો'),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('એગ્રો સ્ટોક (+91 ${widget.userPhone})', style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
        backgroundColor: Colors.green.shade700,
        foregroundColor: Colors.white,
        actions: [
          IconButton(
            icon: const Icon(Icons.logout),
            tooltip: 'લોગઆઉટ',
            onPressed: _logout,
          ),
        ],
      ),
      body: Column(
        children: [
          Padding(
            padding: const EdgeInsets.all(10.0),
            child: TextField(
              decoration: InputDecoration(
                hintText: 'નામ અથવા વજનથી શોધો...',
                prefixIcon: const Icon(Icons.search),
                border: OutlineInputBorder(borderRadius: BorderRadius.circular(10)),
                contentPadding: const EdgeInsets.symmetric(horizontal: 10),
              ),
              onChanged: (val) => setState(() => searchQuery = val),
            ),
          ),
          SingleChildScrollView(
            scrollDirection: Axis.horizontal,
            child: Row(
              children: ['બધા', 'ખાતર', 'બિયારણ', 'દવા'].map((c) {
                return Padding(
                  padding: const EdgeInsets.symmetric(horizontal: 5),
                  child: FilterChip(
                    label: Text(c),
                    selected: selectedCategory == c,
                    selectedColor: Colors.green.shade200,
                    onSelected: (selected) => setState(() => selectedCategory = c),
                  ),
                );
              }).toList(),
            ),
          ),
          const Divider(),
          Expanded(
            child: ListView.builder(
              itemCount: filteredItems.length,
              itemBuilder: (context, index) {
                final item = filteredItems[index];
                return Card(
                  margin: const EdgeInsets.symmetric(horizontal: 10, vertical: 6),
                  elevation: 2,
                  child: Padding(
                    padding: const EdgeInsets.all(12.0),
                    child: Column(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        Row(
                          children: [
                            Expanded(child: Text(item.name, style: const TextStyle(fontSize: 17, fontWeight: FontWeight.bold))),
                            IconButton(
                              icon: const Icon(Icons.edit_note, color: Colors.teal, size: 26),
                              tooltip: 'નામ અને વજન સુધારો',
                              onPressed: () => _editProductDetailsDialog(item),
                            ),
                            Chip(label: Text(item.category), backgroundColor: Colors.green.shade50),
                          ],
                        ),
                        Container(
                          padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 3),
                          decoration: BoxDecoration(color: Colors.orange.shade50, borderRadius: BorderRadius.circular(6)),
                          child: Text('પેકિંગ / વજન: ${item.weight}', style: TextStyle(fontWeight: FontWeight.w600, color: Colors.orange.shade900)),
                        ),
                        const SizedBox(height: 8),
                        Row(
                          mainAxisAlignment: MainAxisAlignment.spaceBetween,
                          children: [
                            Row(
                              children: [
                                Text('ભાવ: ₹${item.price}', style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold, color: Colors.blueAccent)),
                                IconButton(icon: const Icon(Icons.edit, size: 18, color: Colors.blue), onPressed: () => _editPriceDialog(item)),
                              ],
                            ),
                            Row(
                              children: [
                                Text('સ્ટોક: ${item.stock} ${item.unit}', style: TextStyle(fontWeight: FontWeight.bold, color: item.stock <= 5 ? Colors.red : Colors.black87)),
                                IconButton(icon: const Icon(Icons.sync_alt, size: 20, color: Colors.green), onPressed: () => _updateStockDialog(item)),
                              ],
                            ),
                          ],
                        ),
                      ],
                    ),
                  ),
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
