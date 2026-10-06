# kano-life
import 'dart:async';
import 'dart:math';
import 'package:flutter/material.dart';

void main() {
  runApp(const KanoLifestyleApp());
}

class KanoLifestyleApp extends StatelessWidget {
  const KanoLifestyleApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Kano Lifestyle & Discovery',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        brightness: Brightness.dark,
        primaryColor: const Color(0xFF0F9D58),
        scaffoldBackgroundColor: const Color(0xFF121418),
        cardColor: const Color(0xFF1E222A),
        colorScheme: const ColorScheme.dark(
          primary: Color(0xFF0F9D58),
          secondary: Color(0xFFFFB300),
          surface: Color(0xFF1E222A),
        ),
        fontFamily: 'Roboto',
      ),
      home: const MainLifestyleScreen(),
    );
  }
}

// ==========================================
// MODELS & DATA STRUCTURES
// ==========================================

enum Language { english, hausa }

class RealItem {
  final String id;
  final String nameEn;
  final String nameHa;
  final String category;
  final int basePrice;
  final int energyRestore;
  final int hungerRestore;
  final String imageUrl;
  int quantity;

  RealItem({
    required this.id,
    required this.nameEn,
    required this.nameHa,
    required this.category,
    required this.basePrice,
    required this.energyRestore,
    required this.hungerRestore,
    required this.imageUrl,
    this.quantity = 0,
  });

  String getName(Language lang) => lang == Language.english ? nameEn : nameHa;
}

class KanoDistrict {
  final String id;
  final String nameEn;
  final String nameHa;
  final String landmarkEn;
  final String landmarkHa;
  final String descriptionEn;
  final String descriptionHa;
  final String category;
  final String heroImageUrl;
  final int baseFareNaira;
  final int distanceKm;
  final int estMinutes;
  final double surgeMultiplier;

  KanoDistrict({
    required this.id,
    required this.nameEn,
    required this.nameHa,
    required this.landmarkEn,
    required this.landmarkHa,
    required this.descriptionEn,
    required this.descriptionHa,
    required this.category,
    required this.heroImageUrl,
    required this.baseFareNaira,
    required this.distanceKm,
    required this.estMinutes,
    this.surgeMultiplier = 1.0,
  });

  String getName(Language lang) => lang == Language.english ? nameEn : nameHa;
  String getLandmark(Language lang) => lang == Language.english ? landmarkEn : landmarkHa;
  String getDescription(Language lang) => lang == Language.english ? descriptionEn : descriptionHa;

  int get currentFare => (baseFareNaira * surgeMultiplier).round();
}

// ==========================================
// MAIN CONTROLLER SCREEN
// ==========================================

class MainLifestyleScreen extends StatefulWidget {
  const MainLifestyleScreen({Key? key}) : super(key: key);

  @override
  State<MainLifestyleScreen> createState() => _MainLifestyleScreenState();
}

class _MainLifestyleScreenState extends State<MainLifestyleScreen> {
  int _selectedTab = 0;
  Language currentLang = Language.english;

  // Player / Resident State
  int walletBalance = 5500;
  int energyLevel = 85;
  int hungerLevel = 60;
  int healthLevel = 95;
  int currentDay = 1;
  String currentWeather = 'Sunny (34°C)';

  late String currentDistrictId;
  late List<KanoDistrict> districts;
  late List<RealItem> catalog;

  @override
  void initState() {
    super.initState();
    _initDistricts();
    _initCatalog();
    currentDistrictId = 'municipal';

    // Live state depletion timer
    Timer.periodic(const Duration(seconds: 20), (timer) {
      if (mounted) {
        setState(() {
          hungerLevel = (hungerLevel - 2).clamp(0, 100);
          if (hungerLevel < 25) {
            energyLevel = (energyLevel - 4).clamp(0, 100);
          }
        });
      }
    });
  }

  void _initDistricts() {
    districts = [
      KanoDistrict(
        id: 'municipal',
        nameEn: 'Kano Municipal (Old City)',
        nameHa: 'Birnin Kano (Tsohon Garin)',
        landmarkEn: 'Kurmi Market & Emir Palace Gate',
        landmarkHa: 'Kasuwar Kurmi da Fadar Sarki',
        descriptionEn: 'The historic heart of West African trade, famous for ancient city walls, dye pits, and vibrant markets.',
        descriptionHa: 'Cibiyar kasuwanci ta tsohon tarihi a Afirka Yamma, sananniya da ganuwa da marina.',
        category: 'Heritage & Trade',
        heroImageUrl: 'https://images.unsplash.com/photo-1590845947698-8924d7409b56?w=800&q=80',
        baseFareNaira: 0,
        distanceKm: 0,
        estMinutes: 0,
      ),
      KanoDistrict(
        id: 'nassarawa',
        nameEn: 'Nassarawa GRA',
        nameHa: 'Nassarawa GRA',
        landmarkEn: 'Government House & Commercial Banks',
        landmarkHa: 'Gidan Gwamnati da Bankuna',
        descriptionEn: 'Posh residential district featuring serene avenues, corporate hubs, top hotels, and government headquarters.',
        descriptionHa: 'Yanki na alfarma mai kwanciyar hankali, ofisoshi, da masauki na zamani.',
        category: 'Business & Luxury',
        heroImageUrl: 'https://images.unsplash.com/photo-1541888946425-d0fbb186a5b7?w=800&q=80',
        baseFareNaira: 400,
        distanceKm: 6,
        estMinutes: 12,
        surgeMultiplier: 1.2,
      ),
      KanoDistrict(
        id: 'sabongari',
        nameEn: 'Sabon Gari District',
        nameHa: 'Sabon Gari',
        landmarkEn: 'France Road Commercial District',
        landmarkHa: 'Titinta France da Kasuwanci',
        descriptionEn: 'A bustling multi-cultural trade engine filled with electronic markets, restaurants, and nightlife spots.',
        descriptionHa: 'Yanki mai dimbin kasuwanci, lantarki, abinci da hidimomin nishadi.',
        category: 'Commercial Hub',
        heroImageUrl: 'https://images.unsplash.com/photo-1578575437130-527eed3abbec?w=800&q=80',
        baseFareNaira: 300,
        distanceKm: 4,
        estMinutes: 10,
      ),
      KanoDistrict(
        id: 'dala',
        nameEn: 'Dala Hill Area',
        nameHa: 'Dutsen Dala',
        landmarkEn: 'Dala Hill Ancient Monument',
        landmarkHa: 'Tsohon Dutsen Dala',
        descriptionEn: 'Panoramic elevated terrain offering sweeping views of the entire Kano landscape and heritage spots.',
        descriptionHa: 'Dutse mai tsayi sosai wanda ke nuna daukacin garin Kano daga sama.',
        category: 'Tourism & Culture',
        heroImageUrl: 'https://images.unsplash.com/photo-1506744038136-46273834b3fb?w=800&q=80',
        baseFareNaira: 250,
        distanceKm: 5,
        estMinutes: 14,
      ),
      KanoDistrict(
        id: 'tarauni',
        nameEn: 'Tarauni & Gyadi-Gyadi',
        nameHa: 'Tarauni da Gyadi-Gyadi',
        landmarkEn: 'Yaman City Gate Axis',
        landmarkHa: 'Titin Kofar Yaman',
        descriptionEn: 'Key transit corridor connecting residential estates, motor parks, and vibrant roadside markets.',
        descriptionHa: 'Hanya mai muhimmanci da ta hada unguwanni da tashoshin mota.',
        category: 'Transit & Living',
        heroImageUrl: 'https://images.unsplash.com/photo-1519999482648-25049ddd37b1?w=800&q=80',
        baseFareNaira: 350,
        distanceKm: 7,
        estMinutes: 16,
      ),
      KanoDistrict(
        id: 'kumbotso',
        nameEn: 'Kumbotso Industrial Hub',
        nameHa: 'Kumbotso da Challawa',
        landmarkEn: 'Challawa Industrial Layout',
        landmarkHa: 'Wurin Masana\'anta na Challawa',
        descriptionEn: 'The manufacturing power base of Kano with textile factories, tannery centers, and heavy haulage.',
        descriptionHa: 'Muhimmin yankin masana\'antun saka, sarrafa fata, da sauran masana\'antu.',
        category: 'Industrial Zone',
        heroImageUrl: 'https://images.unsplash.com/photo-1581091226825-a6a2a5aee158?w=800&q=80',
        baseFareNaira: 600,
        distanceKm: 12,
        estMinutes: 25,
        surgeMultiplier: 1.15,
      ),
    ];
  }

  void _initCatalog() {
    catalog = [
      RealItem(
        id: 'suya',
        nameEn: 'Special Beef Suya & Onion',
        nameHa: 'Suyan Saniya mai Albasu',
        category: 'Street Food',
        basePrice: 600,
        energyRestore: 30,
        hungerRestore: 45,
        imageUrl: 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?w=400&q=80',
      ),
      RealItem(
        id: 'masa',
        nameEn: 'Hot Masa with Yaji & Meat Sauce',
        nameHa: 'Hot Masa da Miya',
        category: 'Traditional Delicacy',
        basePrice: 350,
        energyRestore: 20,
        hungerRestore: 35,
        imageUrl: 'https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?w=400&q=80',
      ),
      RealItem(
        id: 'kunu',
        nameEn: 'Chilled Kunu Zaki Drink',
        nameHa: 'Kunu Zaki mai Sanyi',
        category: 'Beverage',
        basePrice: 200,
        energyRestore: 25,
        hungerRestore: 15,
        imageUrl: 'https://images.unsplash.com/photo-1513558161293-cdaf765ed2fd?w=400&q=80',
      ),
      RealItem(
        id: 'kilishi',
        nameEn: 'Export Quality Spice Kilishi',
        nameHa: 'Dandantaccen Kilishi',
        category: 'Preserved Snack',
        basePrice: 1200,
        energyRestore: 40,
        hungerRestore: 55,
        imageUrl: 'https://images.unsplash.com/photo-1544025162-d76694265947?w=400&q=80',
      ),
    ];
  }

  void _travelTo(KanoDistrict district) {
    if (district.id == currentDistrictId) return;

    if (walletBalance < district.currentFare) {
      _showSnackBar(
        currentLang == Language.english
            ? 'Insufficient balance for Keke fare (₦${district.currentFare})'
            : 'Kudin ka bai kai kudin Keke ba (₦${district.currentFare})',
        isError: true,
      );
      return;
    }

    if (energyLevel < 8) {
      _showSnackBar(
        currentLang == Language.english ? 'You are too exhausted to travel! Rest first.' : 'Kaji gaba daya! Fara huta.',
        isError: true,
      );
      return;
    }

    setState(() {
      walletBalance -= district.currentFare;
      energyLevel = (energyLevel - 8).clamp(0, 100);
      currentDistrictId = district.id;
    });

    _showSnackBar(
      currentLang == Language.english
          ? 'Arrived at ${district.nameEn} via Keke Napep!'
          : 'Ka isa ${district.nameHa} a cikin Keke!',
    );
  }

  void _showSnackBar(String text, {bool isError = false}) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(text),
        backgroundColor: isError ? Colors.redAccent : const Color(0xFF0F9D58),
        behavior: SnackBarBehavior.floating,
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final activeDistrict = districts.firstWhere((d) => d.id == currentDistrictId);

    return Scaffold(
      body: SafeArea(
        child: Column(
          children: [
            _buildTopAppBar(),
            _buildStatusHeader(),
            Expanded(
              child: IndexedStack(
                index: _selectedTab,
                children: [
                  _buildExploreView(activeDistrict),
                  _buildMapView(),
                  _buildMarketView(),
                  _buildProfileView(),
                ],
              ),
            ),
          ],
        ),
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _selectedTab,
        selectedItemColor: const Color(0xFF0F9D58),
        unselectedItemColor: Colors.grey,
        backgroundColor: const Color(0xFF1E222A),
        type: BottomNavigationBarType.fixed,
        onTap: (index) => setState(() => _selectedTab = index),
        items: [
          BottomNavigationBarItem(
            icon: const Icon(Icons.explore),
            label: currentLang == Language.english ? 'Explore' : 'Bincika',
          ),
          BottomNavigationBarItem(
            icon: const Icon(Icons.map),
            label: currentLang == Language.english ? 'City Map' : 'Taswira',
          ),
          BottomNavigationBarItem(
            icon: const Icon(Icons.storefront),
            label: currentLang == Language.english ? 'Markets' : 'Kasuwanni',
          ),
          BottomNavigationBarItem(
            icon: const Icon(Icons.person),
            label: currentLang == Language.english ? 'Profile' : 'Profile',
          ),
        ],
      ),
    );
  }

  // ==========================================
  // TOP APP BAR & STATUS BAR
  // ==========================================

  Widget _buildTopAppBar() {
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
      color: const Color(0xFF181B20),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Row(
            children: [
              Container(
                padding: const EdgeInsets.all(6),
                decoration: BoxDecoration(
                  color: const Color(0xFF0F9D58).withOpacity(0.2),
                  borderRadius: BorderRadius.circular(8),
                ),
                child: const Icon(Icons.location_city, color: Color(0xFF0F9D58), size: 24),
              ),
              const SizedBox(width: 10),
              Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    currentLang == Language.english ? 'KANO LIFESTYLE' : 'RAYUWAR KANO',
                    style: const TextStyle(fontWeight: FontWeight.bold, fontSize: 16, letterSpacing: 1.1),
                  ),
                  Text(
                    'Day $currentDay • $currentWeather',
                    style: const TextStyle(color: Colors.grey, fontSize: 11),
                  ),
                ],
              ),
            ],
          ),
          OutlinedButton(
            style: OutlinedButton.styleFrom(
              side: const BorderSide(color: Color(0xFF0F9D58)),
              shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(20)),
              padding: const EdgeInsets.symmetric(horizontal: 12, vertical: 4),
            ),
            onPressed: () {
              setState(() {
                currentLang = currentLang == Language.english ? Language.hausa : Language.english;
              });
            },
            child: Text(
              currentLang == Language.english ? '🇳🇬 HAUSA' : '🇬🇧 ENGLISH',
              style: const TextStyle(fontSize: 11, fontWeight: FontWeight.bold, color: Color(0xFF0F9D58)),
            ),
          ),
        ],
      ),
    );
  }

  Widget _buildStatusHeader() {
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 10),
      color: const Color(0xFF1E222A),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Row(
            children: [
              const Icon(Icons.account_balance_wallet, color: Color(0xFFFFB300), size: 20),
              const SizedBox(width: 6),
              Text(
                '₦$walletBalance',
                style: const TextStyle(fontSize: 18, fontWeight: FontWeight.bold, color: Color(0xFFFFB300)),
              ),
            ],
          ),
          Row(
            children: [
              _buildMiniStat('⚡', '$energyLevel%', Colors.amber),
              const SizedBox(width: 12),
              _buildMiniStat('🍗', '$hungerLevel%', Colors.deepOrange),
              const SizedBox(width: 12),
              _buildMiniStat('❤️', '$healthLevel%', Colors.redAccent),
            ],
          ),
        ],
      ),
    );
  }

  Widget _buildMiniStat(String icon, String label, Color color) {
    return Row(
      children: [
        Text(icon, style: const TextStyle(fontSize: 13)),
        const SizedBox(width: 4),
        Text(
          label,
          style: TextStyle(fontSize: 12, fontWeight: FontWeight.bold, color: color),
        ),
      ],
    );
  }

  // ==========================================
  // TAB 1: EXPLORE VIEW
  // ==========================================

  Widget _buildExploreView(KanoDistrict active) {
    return SingleChildScrollView(
      physics: const BouncingScrollPhysics(),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          // Hero Banner
          Stack(
            children: [
              Container(
                height: 220,
                width: double.infinity,
                decoration: BoxDecoration(
                  image: DecorationImage(
                    image: NetworkImage(active.heroImageUrl),
                    fit: BoxFit.cover,
                  ),
                ),
              ),
              Container(
                height: 220,
                decoration: BoxDecoration(
                  gradient: LinearGradient(
                    colors: [Colors.transparent, const Color(0xFF121418).withOpacity(0.95)],
                    begin: Alignment.topCenter,
                    end: Alignment.bottomCenter,
                  ),
                ),
              ),
              Positioned(
                bottom: 16,
                left: 16,
                right: 16,
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Container(
                      padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 4),
                      decoration: BoxDecoration(
                        color: const Color(0xFF0F9D58),
                        borderRadius: BorderRadius.circular(12),
                      ),
                      child: Text(
                        active.category,
                        style: const TextStyle(fontSize: 11, fontWeight: FontWeight.bold),
                      ),
                    ),
                    const SizedBox(height: 6),
                    Text(
                      active.getName(currentLang),
                      style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
                    ),
                    Row(
                      children: [
                        const Icon(Icons.place, size: 14, color: Colors.grey),
                        const SizedBox(width: 4),
                        Expanded(
                          child: Text(
                            active.getLandmark(currentLang),
                            style: const TextStyle(color: Colors.grey, fontSize: 13),
                            maxLines: 1,
                            overflow: TextOverflow.ellipsis,
                          ),
                        ),
                      ],
                    ),
                  ],
                ),
              ),
            ],
          ),

          Padding(
            padding: const EdgeInsets.all(16.0),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  currentLang == Language.english ? 'District Overview' : 'Bayanin Yanki',
                  style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
                ),
                const SizedBox(height: 6),
                Text(
                  active.getDescription(currentLang),
                  style: const TextStyle(color: Colors.grey, height: 1.4),
                ),
                const SizedBox(height: 20),

                // Quick City Services / Actions
                Text(
                  currentLang == Language.english ? 'Available Lifestyle Activities' : 'Ayyukan Wurin',
                  style: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
                ),
                const SizedBox(height: 12),

                _buildActionCard(
                  title: currentLang == Language.english ? 'Drive Keke Napep Route' : 'Tuka Keke Napep',
                  subtitle: curr