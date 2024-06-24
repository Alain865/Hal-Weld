# Hal-Weld
Aplicativo da WALTech 
import 'dart:convert';
import 'dart:typed_data';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'package:image_picker/image_picker.dart';


void main() {
  runApp(const MeuApp());
}

class MeuApp extends StatelessWidget {
  const MeuApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'HAL Weld',
      theme: ThemeData(
        
        appBarTheme: const AppBarTheme(
          backgroundColor: Colors.transparent,
          elevation: 0,
        ),
        scaffoldBackgroundColor: const Color.fromARGB(255, 232, 244, 232),
        primarySwatch: Colors.green,
        textTheme: const TextTheme(
          bodyLarge: TextStyle(fontSize: 16, color: Color.fromARGB(255, 0, 0, 0)),
          bodyMedium: TextStyle(fontSize: 14, color: Color.fromARGB(255, 0, 0, 0)),
          displayLarge: TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
          displayMedium: TextStyle(fontSize: 20, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
        ),
        elevatedButtonTheme: ElevatedButtonThemeData(
          style: ElevatedButton.styleFrom(
            backgroundColor: const Color.fromARGB(255, 255, 255, 255),
            foregroundColor: Colors.green,
            textStyle: const TextStyle(fontSize: 18),
            padding: const EdgeInsets.symmetric(horizontal: 40, vertical: 16),
            shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(20)),
          ),
        ),
        inputDecorationTheme: InputDecorationTheme(
          filled: true,
          fillColor: Colors.white,
          border: OutlineInputBorder(
            borderRadius: BorderRadius.circular(10),
          ),
          focusedBorder: OutlineInputBorder(
            borderRadius: BorderRadius.circular(10),
            borderSide: const BorderSide(color: Colors.green),
          ),
        ),
      ),
      home: const MinhaPaginaInicial(),
    );
  }
}

class MinhaPaginaInicial extends StatefulWidget {
  const MinhaPaginaInicial({super.key});

  @override
  State<MinhaPaginaInicial> createState() => _MinhaPaginaInicialEstado();
}

class _MinhaPaginaInicialEstado extends State<MinhaPaginaInicial> {
  final GlobalKey<ScaffoldState> _scaffoldKey = GlobalKey<ScaffoldState>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      key: _scaffoldKey,
      appBar: AppBar(
        title: const Text('HAL Weld'),
        backgroundColor: const Color.fromARGB(0, 255, 255, 255),
        elevation: 0,
      ),
      drawer: Drawer(
        child: ListView(
          padding: EdgeInsets.zero,
          children: [
            const DrawerHeader(
              decoration: BoxDecoration(
                color: Color.fromARGB(255, 89, 189, 91),
              ),
              child: Text(
                'Menu',
                style: TextStyle(
                  color: Color.fromARGB(255, 0, 0, 0),
                  fontSize: 24,
                ),
              ),
            )
            ,
            ListTile(
              leading: const Icon(Icons.home),
              title: const Text('Home'),
              onTap: () {
                Navigator.pushReplacement(
                  context,
                  MaterialPageRoute(builder: (context) => const MinhaPaginaInicial()),
                );
              },
            ),
            ListTile(
              leading: const Icon(Icons.assessment),
              title: const Text('Classificação de Soldas'),
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => const ClassificacaoDeSoldas()),
                );
              },
            ),
                        ListTile(
              leading: const Icon(Icons.history),
              title: const Text('Histórico de Soldas Classificadas'),
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => const HistoricoPage()),
                );
              },
            ),
            ListTile(
              leading: const Icon(Icons.build),
              title: const Text('Parâmetros de Soldagem'),
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => const ParametrosPage()),
                );
              },
            ),
            ListTile(
              leading: const Icon(Icons.book),
              title: const Text('Normas'),
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => const NormasPage()),
                );
              },
            ),
            ListTile(
              leading: const Icon(Icons.info),
              title: const Text('Sobre a Empresa'),
              onTap: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => const SobrePage()),
                );
              },
            ),
          ],
        ),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text(
              'Bem-vindo ao App da HALLTech Solutins',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 20),
            ClipRRect(
              borderRadius: BorderRadius.circular(20.0),
              child: Image.asset(
                'assets/imagens/sobre_a_empresa.jpeg',
                width: 300,
                height: 300,
                fit: BoxFit.cover,
              ),
            ),
            const SizedBox(height: 20),
            const Text(
              'Este aplicativo permite a classificação de soldas, consulta de parâmetros e normas, e mantém um histórico das soldas avaliadas. Ele ajuda a garantir a qualidade e a conformidade no seu trabalho diário.',
              style: TextStyle(fontSize: 16, color: Color.fromARGB(255, 0, 0, 0)),
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                _scaffoldKey.currentState?.openDrawer();
              },
              child: const Text('Abrir Menu'),
            ),
          ],
        ),
      ),
    );
  }
}

class ClassificacaoDeSoldas extends StatefulWidget {
  const ClassificacaoDeSoldas({super.key});

  @override
  State<ClassificacaoDeSoldas> createState() => _ClassificacaoDeSoldasEstado();
}

class _ClassificacaoDeSoldasEstado extends State<ClassificacaoDeSoldas> {
  String? _resultadoClassificacao;
  String? _confianca;
  bool _carregando = false;
  Uint8List? _imagemBytes;

Future<void> selecionarEAnalisarImagem(ImageSource source) async {
  final ImagePicker picker = ImagePicker();
  final XFile? arquivoImagem = await picker.pickImage(source: source);

  if (arquivoImagem != null) {
    final bytesImagem = await arquivoImagem.readAsBytes();
    setState(() {
      _imagemBytes = bytesImagem;
      _carregando = true;
    });

    try {
      final resultado = await WeldAnalyzerService.analisarSolda(arquivoImagem);
      print('Resposta da API: ${resultado.toString()}'); // Debug
      if (resultado != null && resultado.containsKey('predictions') && resultado['predictions'].isNotEmpty) {
        setState(() {
          _resultadoClassificacao = resultado['predictions'][0]['tagName'];
          _confianca = (resultado['predictions'][0]['probability'] * 100).toStringAsFixed(2) + '%';
          _carregando = false;
        });
        HistoricoService.addHistorico({
          'resultado': _resultadoClassificacao!,
          'confianca': _confianca!,
          'data': DateTime.now().toIso8601String(),
        });
      } else {
        throw Exception('Dados de resposta inválidos.');
      }
    } catch (e) {
      setState(() {
        _carregando = false;
      });
      showDialog(
        context: context,
        builder: (context) => AlertDialog(
          title: Text('Erro'),
          content: Text('Não foi possível classificar a imagem. Tente novamente. Erro: $e'),
          actions: <Widget>[
            TextButton(
              child: Text('OK'),
              onPressed: () => Navigator.of(context).pop(),
            ),
          ],
        ),
      );
    }
  }
}


  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Classificação de Soldas'),
        backgroundColor: Colors.transparent,
        elevation: 0,
      ),
      body: Center(
        child: _carregando
            ? const CircularProgressIndicator()
            : SingleChildScrollView(
                padding: const EdgeInsets.all(16.0),
                child: Column(
                  mainAxisAlignment: MainAxisAlignment.center,
                  children: [
                    const Text(
                      'Classificador de Soldas',
                      style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
                      textAlign: TextAlign.center,
                    ),
                    const SizedBox(height: 20),
                    const Text(
                      'Posicione a peça de modo que a solda esteja visível, tire uma foto ou anexe uma imagem existente.',
                      style: TextStyle(fontSize: 16, color: Color.fromARGB(255, 0, 0, 0)),
                      textAlign: TextAlign.center,
                    ),
                    const SizedBox(height: 20),
                    if (_imagemBytes != null)
                      Image.memory(
                        _imagemBytes!,
                        height: 200,
                      ),
                    const SizedBox(height: 20),
                    ElevatedButton(
                      onPressed: () {
                        selecionarEAnalisarImagem(ImageSource.camera);
                      },
                      child: const Text('Tirar Foto'),
                    ),
                    const SizedBox(height: 20),
                    ElevatedButton(
                      onPressed: () {
                        selecionarEAnalisarImagem(ImageSource.gallery);
                      },
                      child: const Text('Selecionar da Galeria'),
                    ),
                    if (_resultadoClassificacao != null && _confianca != null)
                      Padding(
                        padding: const EdgeInsets.only(top: 20),
                        child: Column(
                          children: [
                            Text(
                              'Resultado: $_resultadoClassificacao',
                              style: const TextStyle(fontSize: 20, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
                              textAlign: TextAlign.center,
                            ),
                            const SizedBox(height: 10),
                            Text(
                              'Confiança: $_confianca',
                              style: const TextStyle(fontSize: 16, color: Color.fromARGB(255, 0, 0, 0)),
                              textAlign: TextAlign.center,
                            ),
                          ],
                        ),
                      ),
                  ],
                ),
              ),
      ),
    );
  }
}

class ParametrosPage extends StatefulWidget {
  const ParametrosPage({super.key});

  @override
  State<ParametrosPage> createState() => _ParametrosPageState();
}

class _ParametrosPageState extends State<ParametrosPage> {
  final TextEditingController _pecaController = TextEditingController();
  Map<String, String>? _parametros;
  String? _fotoPeca;

  final List<Map<String, dynamic>> _pecas = [
    {
      'nome': 'Cilindro Menor',
      'parametros': {'Corrente': '10A', 'Voltagem': '220V', 'Velocidade': '150mm/min', 'Espessura da Raiz': '5mm', 'Quantidade de Passes': '3'},
      'foto': 'assets/imagens/cilindro_menor.png',
    }, 
    {
      'nome': 'Cone Traseiro',
      'parametros': {'Corrente': '15A', 'Voltagem': '220V', 'Velocidade': '200mm/min', 'Espessura da Raiz': '5mm', 'Quantidade de Passes': '3'},
      'foto': 'assets/imagens/cone_traseiro.png',
    },
        {
      'nome': 'Aro de Solda',
      'parametros': {'Corrente': '15A', 'Voltagem': '220V', 'Velocidade': '150mm/min', 'Espessura da Raiz': '5mm', 'Quantidade de Passes': '4'},
      'foto': 'assets/imagens/aro_de_solda.png',
    }, 
    {
      'nome': 'Calota',
      'parametros': {'Corrente': '15A', 'Voltagem': '220V', 'Velocidade': '100mm/min', 'Espessura da Raiz': '5mm', 'Quantidade de Passes': '3'},
      'foto': 'assets/imagens/calota.png',
    },
        {
      'nome': 'Cilindro Maior',
      'parametros': {'Corrente': '10A', 'Voltagem': '220V', 'Velocidade': '150mm/min', 'Espessura da Raiz': '6mm', 'Quantidade de Passes': '5'},
      'foto': 'assets/imagens/cilindro_menor.png',
    }, 
    {
      'nome': 'Corpo da Boca',
      'parametros': {'Corrente': '15A', 'Voltagem': '220V', 'Velocidade': '200mm/min', 'Espessura da Raiz': '-', 'Quantidade de Passes': '4'},
      'foto': 'assets/imagens/corpo_da_boca.png',
    },
        {
      'nome': 'Cupula',
      'parametros': {'Corrente': '10A', 'Voltagem': '220V', 'Velocidade': '150mm/min', 'Espessura da Raiz': '6mm', 'Quantidade de Passes': '3'},
      'foto': 'assets/imagens/cupula.png',
    }, 
    {
      'nome': 'Flange Quadrada Cega',
      'parametros': {'Corrente': 'não se aplica', 'Voltagem': 'não se aplica', 'Velocidade': 'não se aplica', 'Espessura da Raiz': 'não se aplica', 'Quantidade de Passes': 'não se aplica'},
      'foto': 'assets/imagens/flange_quadrada_cega.png',
    },
        {
      'nome': 'Flange Quadrada',
      'parametros': {'Corrente': '10A', 'Voltagem': '220V', 'Velocidade': '150mm/min', 'Espessura da Raiz': '-', 'Quantidade de Passes': '4'},
      'foto': 'assets/imagens/flange_quadrada.png',
    }, 
    {
      'nome': 'Flange Redonda',
      'parametros': {'Corrente': '15A', 'Voltagem': '220V', 'Velocidade': '200mm/min', 'Espessura da Raiz': '-', 'Quantidade de Passes': '3'},
      'foto': 'assets/imagens/flange_redonda.png',
    },
        {
      'nome': 'Gomo da Cupula',
      'parametros': {'Corrente': '10A', 'Voltagem': '220V', 'Velocidade': '150mm/min', 'Espessura da Raiz': '5mm', 'Quantidade de Passes': '1'},
      'foto': 'assets/imagens/gomo_da_cupula.png',
    }, 
    {
      'nome': 'Moldura de Encaixe',
      'parametros': {'Corrente': '15A', 'Voltagem': '220V', 'Velocidade': '200mm/min', 'Espessura da Raiz': '-', 'Quantidade de Passes': '4'},
      'foto': 'assets/imagens/moldura_de_encaixe.png',
    },
  ];

  void _buscarParametros() {
    final peca = _pecas.firstWhere(
        (peca) => peca['nome'] == _pecaController.text,
        orElse: () => {});

    setState(() {
      if (peca.isNotEmpty) {
        _parametros = Map<String, String>.from(peca['parametros']);
        _fotoPeca = peca['foto'];

      } else {
        _parametros = null;
        _fotoPeca = null;
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Parâmetros'),
        backgroundColor: const Color.fromARGB(255, 255, 255, 255),
        elevation: 0,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text(
              'Consulta de Parâmetros',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 20),
            const Text(
              'Digite o nome da peça para consultar os parâmetros.',
              style: TextStyle(fontSize: 16, color: Color.fromARGB(255, 0, 0, 0)),
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 20),
            TextField(
              controller: _pecaController,
              decoration: const InputDecoration(
                labelText: 'Nome da Peça',
              ),
              style: const TextStyle(color: Color.fromARGB(255, 8, 8, 8)),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: _buscarParametros,
              child: const Text('Buscar Parâmetros'),
            ),

            const SizedBox(height: 20),
            if (_parametros != null)
              Column(
                children: [
                  if (_fotoPeca != null)
                    Image.asset(
                      _fotoPeca!,
                      height: 200, // Altura fixa para a imagem menor
                    ),
                  Card(
                    color: const Color.fromARGB(255, 174, 174, 174),
                    child: Padding(
                      padding: const EdgeInsets.all(16.0),
                      child: Column(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                          Text('Corrente: ${ _parametros!['Corrente']}'),
                          Text('Voltagem: ${ _parametros!['Voltagem']}'),
                          Text('Velocidade: ${ _parametros!['Velocidade']}'),
                          Text('Espessura da Raiz: ${ _parametros!['Espessura da Raiz']}'),
                          Text('Quantidade de Passes: ${ _parametros!['Quantidade de Passes']}'),
                        ],
                      ),
                    ),
                  ),
                ],
              ),
            const SizedBox(height: 20),
            const Text(
              'Lista de Peças Disponíveis:',
              style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
            ),
            const SizedBox(height: 10),
            Expanded(
              child: ListView.builder(
                itemCount: _pecas.length,
                itemBuilder: (context, index) {
                  return ListTile(
                    title: Text(_pecas[index]['nome'], style: const TextStyle(color: Color.fromARGB(255, 0, 0, 0))),
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}


class NormasPage extends StatefulWidget {
  const NormasPage({super.key});

  @override
  _NormasPageState createState() => _NormasPageState();
}

class _NormasPageState extends State<NormasPage> {
  final TextEditingController _controller = TextEditingController();
  String _nrText = "";
  final Map<String, String> _nrDescriptions = {
    
  "NR-1": "NR-1 - Disposições Gerais e Gerenciamento de Riscos Ocupacionais.\n"
          "Estabelece os requisitos mínimos para garantir a segurança e a saúde dos trabalhadores. "
          "Define a gestão de riscos ocupacionais, com ênfase na prevenção de acidentes e doenças no trabalho.",
  
  "NR-2": "NR-2 - Inspeção Prévia.\n"
          "Regulamenta a inspeção de projetos e instalações para verificar a conformidade com as normas de segurança antes da execução das obras ou operações.",
  
  "NR-3": "NR-3 - Embargo e Interdição.\n"
          "Determina as medidas de embargo ou interdição de locais de trabalho que apresentem risco iminente à segurança e saúde dos trabalhadores.",
  
  "NR-4": "NR-4 - Serviços Especializados em Engenharia de Segurança e em Medicina do Trabalho (SESMT).\n"
          "Define a estrutura mínima de SESMT nas empresas, estabelecendo as responsabilidades dos profissionais de segurança e medicina do trabalho.",
  
  "NR-5": "NR-5 - Comissão Interna de Prevenção de Acidentes (CIPA).\n"
          "Institui a CIPA, um órgão interno responsável por promover a segurança e saúde no trabalho. Define os critérios de composição e funcionamento da CIPA.",
  
  "NR-6": "NR-6 - Equipamento de Proteção Individual (EPI).\n"
          "Estabelece as diretrizes para a utilização de EPIs, especificando os critérios para seleção, uso, conservação e higienização dos equipamentos de proteção.",
  
  "NR-7": "NR-7 - Programas de Controle Médico de Saúde Ocupacional (PCMSO).\n"
          "Determina a obrigatoriedade de programas de controle médico de saúde ocupacional, visando à promoção e preservação da saúde dos trabalhadores.",
  
  "NR-8": "NR-8 - Edificações.\n"
          "Define os requisitos de segurança e saúde no trabalho em atividades relacionadas à construção e manutenção de edificações, incluindo as medidas de prevenção e controle de riscos.",
  
  "NR-9": "NR-9 - Programa de Prevenção de Riscos Ambientais (PPRA).\n"
          "Estabelece a obrigatoriedade de implementação de programas de prevenção de riscos ambientais, visando à preservação da saúde e integridade física dos trabalhadores.",
  
  "NR-10": "NR-10 - Segurança em Instalações e Serviços em Eletricidade.\n"
           "Regulamenta as atividades e serviços em instalações elétricas, estabelecendo os requisitos mínimos para garantir a segurança dos trabalhadores.",
  
  "NR-11": "NR-11 - Transporte, Movimentação, Armazenagem e Manuseio de Materiais.\n"
           "Estabelece os requisitos para garantir a segurança nas atividades de transporte, movimentação, armazenamento e manuseio de materiais.",
  
  "NR-12": "NR-12 - Segurança no Trabalho em Máquinas e Equipamentos.\n"
           "Define os requisitos mínimos de segurança para a fabricação, montagem, operação e manutenção de máquinas e equipamentos, visando à proteção dos trabalhadores.",
  
  "NR-13": "NR-13 - Caldeiras, Vasos de Pressão e Tubulações.\n"
           "Estabelece as normas de segurança para a instalação, operação e manutenção de caldeiras, vasos de pressão e tubulações, visando prevenir acidentes.",
  
  "NR-14": "NR-14 - Fornos.\n"
           "Determina as medidas de segurança para a instalação, operação e manutenção de fornos, prevendo a proteção dos trabalhadores contra riscos específicos.",
  
  "NR-15": "NR-15 - Atividades e Operações Insalubres.\n"
           "Define as atividades e operações consideradas insalubres, especificando os limites de tolerância de agentes ambientais nocivos à saúde.",
  
  "NR-16": "NR-16 - Atividades e Operações Perigosas.\n"
           "Estabelece os critérios para caracterização das atividades e operações perigosas, determinando as condições e requisitos para o pagamento do adicional de periculosidade.",
  
  "NR-17": "NR-17 - Ergonomia.\n"
           "Define os parâmetros para a adaptação das condições de trabalho às características psicofisiológicas dos trabalhadores, visando à melhoria das condições de trabalho e à prevenção de doenças.",
  
  "NR-18": "NR-18 - Condições e Meio Ambiente de Trabalho na Indústria da Construção.\n"
           "Estabelece as condições e requisitos mínimos de segurança e saúde no trabalho na indústria da construção, abrangendo desde a execução das obras até a conclusão dos serviços.",
  
  "NR-19": "NR-19 - Explosivos.\n"
           "Regulamenta o uso, manuseio, armazenamento e transporte de explosivos, com o objetivo de prevenir acidentes e garantir a segurança dos trabalhadores.",
  
  "NR-20": "NR-20 - Segurança e Saúde no Trabalho com Inflamáveis e Combustíveis.\n"
           "Estabelece as normas para a segurança e saúde no trabalho com inflamáveis e combustíveis, definindo medidas preventivas e de controle de riscos.",
  
  "NR-21": "NR-21 - Trabalhos a Céu Aberto.\n"
           "Determina as normas de segurança e saúde para os trabalhos realizados a céu aberto, estabelecendo medidas de controle de riscos e condições adequadas de trabalho.",
  
  "NR-22": "NR-22 - Segurança e Saúde Ocupacional na Mineração.\n"
           "Define as normas de segurança e saúde para as atividades de mineração, abrangendo desde a pesquisa mineral até a lavra e beneficiamento dos minérios.",
  
  "NR-23": "NR-23 - Proteção Contra Incêndios.\n"
           "Estabelece as medidas de prevenção e combate a incêndios, determinando os requisitos para a instalação, manutenção e uso de equipamentos de combate a incêndios.",
  
  "NR-24": "NR-24 - Condições Sanitárias e de Conforto nos Locais de Trabalho.\n"
           "Define os requisitos mínimos de instalações sanitárias e de conforto nos locais de trabalho, visando à promoção da saúde e bem-estar dos trabalhadores.",
  
  "NR-25": "NR-25 - Resíduos Industriais.\n"
           "Estabelece as normas para a gestão dos resíduos industriais, incluindo sua classificação, armazenamento, transporte e descarte adequado.",
  
  "NR-26": "NR-26 - Sinalização de Segurança.\n"
           "Define os requisitos para a sinalização de segurança nos locais de trabalho, incluindo os tipos, formatos, cores e posições das sinalizações.",
  
  "NR-27": "NR-27 - Registro Profissional do Técnico de Segurança do Trabalho no Ministério do Trabalho.\n"
           "Regulamenta a obrigatoriedade do registro profissional dos técnicos de segurança do trabalho no Ministério do Trabalho, estabelecendo as exigências para a habilitação e fiscalização.",
  
  "NR-28": "NR-28 - Fiscalização e Penalidades.\n"
           "Estabelece as normas para a fiscalização e aplicação de penalidades em caso de descumprimento das normas regulamentadoras, detalhando os procedimentos e critérios de penalização.",
  
  "NR-29": "NR-29 - Segurança e Saúde no Trabalho Portuário.\n"
           "Determina as normas de segurança e saúde no trabalho nos portos, estabelecendo medidas preventivas e de controle de riscos específicos das atividades portuárias.",
  
  "NR-30": "NR-30 - Segurança e Saúde no Trabalho Aquaviário.\n"
           "Define os requisitos de segurança e saúde no trabalho para as atividades aquaviárias, abrangendo embarcações e instalações portuárias.",
  
  "NR-31": "NR-31 - Segurança e Saúde no Trabalho na Agricultura, Pecuária, Silvicultura, Exploração Florestal e Aquicultura.\n"
           "Estabelece as normas de segurança e saúde para as atividades agrícolas, pecuárias, florestais e de aquicultura, visando à proteção dos trabalhadores e ao controle de riscos específicos.",
  
  "NR-32": "NR-32 - Segurança e Saúde no Trabalho em Serviços de Saúde.\n"
           "Regulamenta as condições de segurança e saúde no trabalho nos serviços de saúde, incluindo hospitais, clínicas e laboratórios, estabelecendo medidas de prevenção e controle de riscos.",
  
  "NR-33": "NR-33 - Segurança e Saúde nos Trabalhos em Espaços Confinados.\n"
           "Define as normas de segurança e saúde para os trabalhos realizados em espaços confinados, estabelecendo requisitos para a identificação, avaliação e controle de riscos.",
  
  "NR-34": "NR-34 - Condições e Meio Ambiente de Trabalho na Indústria da Construção e Reparação Naval.\n"
           "Estabelece as normas de segurança e saúde para as atividades de construção e reparação naval, abrangendo todos os processos e atividades envolvidas na indústria naval.",
  
  "NR-35": "NR-35 - Trabalho em Altura.\n"
           "Regulamenta as atividades realizadas em altura, estabelecendo os requisitos mínimos para a prevenção de acidentes e a proteção dos trabalhadores que executam essas atividades.",
           
  "NR-37": "NR-37 - Segurança e Saúde em Plataformas de Petróleo.\n"
           "Estabelece os requisitos mínimos de segurança e saúde para o trabalho em plataformas de petróleo, abrangendo desde a construção até a operação e manutenção dessas estruturas, visando à prevenção de acidentes e doenças ocupacionais.",

  "NR-38": "NR-38 - Segurança e Saúde no Trabalho em Ambientes Hiperbáricos.\n"
           "Regulamenta as condições de segurança e saúde no trabalho em ambientes hiperbáricos, como câmaras hiperbáricas e trabalhos submersos, estabelecendo medidas de prevenção e controle de riscos específicos.",

  "ASME IX": "ASME IX - Qualificação de Soldadores e Procedimentos de Soldagem.\n"
           "Define os requisitos para a qualificação de soldadores e os procedimentos de soldagem, visando garantir a qualidade e a segurança das soldagens realizadas em equipamentos e estruturas.",

  "ASME VIII": "ASME VIII - Vasos de Pressão.\n"
           "Estabelece os requisitos de projeto, fabricação, inspeção, teste e certificação de vasos de pressão, garantindo que atendam aos padrões de segurança e desempenho.",

  "ASME V": "ASME V - Testes Não Destrutivos.\n"
            "Regulamenta os métodos e procedimentos para a realização de testes não destrutivos em materiais e componentes, visando à detecção de falhas e à garantia da integridade dos produtos.",

  "ASME II": "ASME II - Materiais.\n"
           "Fornece especificações detalhadas para materiais utilizados na fabricação de caldeiras, vasos de pressão e outros equipamentos, garantindo que atendam aos requisitos de desempenho e segurança.",

  "AWS A5.12-92": "AWS A5.12-92 - Especificação para Varetas de Soldagem TIG e Arame de Soldagem para Alumínio e Ligas de Alumínio.\n"
           "Estabelece as especificações para as varetas de soldagem TIG e os arames de soldagem utilizados na soldagem de alumínio e suas ligas, garantindo a qualidade e a consistência dos materiais de soldagem."
  
  "NBR 5410" "NBR 5410 - Instalações Elétricas de Baixa Tensão.\n"
           "Estabelece as condições técnicas e os requisitos mínimos para o projeto, execução, verificação e manutenção das instalações elétricas de baixa tensão, visando garantir a segurança das pessoas e a preservação dos bens.",

  "NBR 5469": "NBR 5469 - Sinalização de Segurança contra Incêndio e Pânico.\n"
            "Define os requisitos para a sinalização de segurança contra incêndio e pânico, incluindo os critérios para a escolha, instalação, e manutenção das sinalizações de emergência em edificações.",

  "NBR 5419": "NBR 5419 - Proteção contra Descargas Atmosféricas.\n"
           "Estabelece os requisitos para a proteção de estruturas contra descargas atmosféricas, abrangendo o projeto, instalação e manutenção dos sistemas de proteção, visando a segurança das edificações e das pessoas.",

  "NBR 5626": "NBR 5626 - Instalação Predial de Água Fria.\n"
           "Especifica as condições técnicas para a instalação predial de água fria, garantindo a segurança, eficiência e durabilidade do sistema, desde a entrada de água na edificação até os pontos de consumo.",

  "NBR 5413": "NBR 5413 - Iluminância de Interiores.\n"
           "Define os valores de iluminância adequados para diferentes tipos de ambientes interiores, visando proporcionar condições visuais confortáveis e seguras para a realização de atividades.",

  "NBR 9050": "NBR 9050 - Acessibilidade a Edificações, Mobiliário, Espaços e Equipamentos Urbanos.\n"
           "Estabelece os critérios e parâmetros técnicos que asseguram a acessibilidade de pessoas com deficiência ou mobilidade reduzida a edificações, mobiliário, espaços e equipamentos urbanos.",

  "NBR 7199": "NBR 7199 - Vidros na Construção Civil.\n"
           "Especifica os requisitos para o uso de vidros na construção civil, abrangendo os critérios de seleção, instalação e segurança, visando garantir a integridade estrutural e a proteção dos usuários.",

  "NBR 12235": "NBR 12235 - Sistema de Proteção por Extintores de Incêndio.\n"
           "Estabelece os critérios para a seleção, instalação, manutenção e inspeção de extintores de incêndio, visando garantir sua eficácia e disponibilidade em situações de emergência.",

  "NBR 7500": "NBR 7500 - Identificação para o Transporte Terrestre, Manuseio, Movimentação e Armazenagem de Produtos.\n"
           "Define os critérios para a identificação adequada de produtos perigosos durante o transporte terrestre, manuseio, movimentação e armazenagem, visando garantir a segurança e a conformidade com as regulamentações."        
  };

    void _searchNR() {
    final query = _controller.text.trim();
    setState(() {
      _nrText = _nrDescriptions[query] ?? "Norma não encontrada.";
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Pesquisar Normas'),
        centerTitle: true,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            TextField(
              controller: _controller,
              decoration: InputDecoration(
                labelText: 'Digite a Norma (ex: NR-1; NBR 7500; ASME IX)',
                labelStyle: const TextStyle(color: Colors.black),
                border: OutlineInputBorder(
                  borderRadius: BorderRadius.circular(8.0),
                ),
              ),
              style: const TextStyle(color: Colors.black),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _searchNR,
              style: ElevatedButton.styleFrom(
                padding: const EdgeInsets.symmetric(horizontal: 24, vertical: 12),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(8.0),
                ),
              ),
              child: const Text('Pesquisar'),
            ),
            const SizedBox(height: 16),
            if (_nrText.isNotEmpty)
              Column(
                crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  Container(
                    padding: const EdgeInsets.all(12.0),
                    decoration: BoxDecoration(
                      color: const Color.fromARGB(255, 174, 174, 174),
                      borderRadius: BorderRadius.circular(8.0),
                    ),
                    child: Text(
                      _nrText,
                      style: Theme.of(context).textTheme.bodyLarge,
                    ),
                  ),
                  const SizedBox(height: 16),
                ],
              ),
            const SizedBox(height: 24),
            const Text(
              'Normas disponíveis para pesquisa:',
              style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 8),
            const Text('• Todas as NRs (Normas Regulamentadoras)'),
            const Text('• NBRs (Normas Brasileiras) 5410; 5469; 5419; 5626; 5413; 9050; 7199; 12235; 7500'),
            const Text('• ASME (Normas da American Society of Mechanical Engineers) II; V; VIII e IX'),
            const Text('• AWS A5.12-92'),
          ],
        ),
      ),
    );
  }
}


class HistoricoPage extends StatelessWidget {
  const HistoricoPage({super.key});

  @override
  Widget build(BuildContext context) {
    final historico = HistoricoService.getHistorico();
    return Scaffold(
      appBar: AppBar(
        title: const Text('Histórico de Soldas Avaliadas'),
        backgroundColor: Colors.transparent,
        elevation: 0,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: ListView.builder(
          itemCount: historico.length,
          itemBuilder: (context, index) {
            final item = historico[index];
            return Card(
              color: const Color.fromARGB(255, 255, 255, 255),
              child: ListTile(
                title: Text('Resultado: ${item['resultado']}'),
                subtitle: Text('Confiança: ${item['confianca']}%'),
                trailing: Text(item['data']!),
              ),
            );
          },
        ),
      ),
    );
  }
}

class SobrePage extends StatelessWidget {
  const SobrePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Sobre a Empresa'),
        backgroundColor: const Color.fromARGB(255, 250, 249, 249),
        elevation: 0,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            const Text(
              'HALLTech Solutions',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: Color.fromARGB(255, 0, 0, 0)),
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 20),
            const SizedBox(height: 20),
            ClipRRect(
              borderRadius: BorderRadius.circular(20.0),
              child: Image.asset(
                'assets/imagens/tela_inicial.jpg',
                width: 300,
                height: 300,
                fit: BoxFit.cover,
              ),
            ),
            const Text(
              'HALLTech Solutions é líder no desenvolvimento de soluções tecnológicas para a indústria de soldagem. Nossa missão é fornecer ferramentas avançadas que garantam a qualidade e eficiência no processo de soldagem.',
              style: TextStyle(fontSize: 16, color: Color.fromARGB(255, 0, 0, 0)),
              textAlign: TextAlign.center,
            ),
          ],
        ),
      ),
    );
  }
}

class WeldAnalyzerService {
  static Future<Map<String, dynamic>> analisarSolda(XFile imagem) async {
    final url = Uri.parse('https://aplicativodetestesoldagem-prediction.cognitiveservices.azure.com/customvision/v3.0/Prediction/5b6cf709-dcab-4053-9657-076528e32d54/classify/iterations/Iteration9/image');
    final headers = {
      'Prediction-Key': 'b6892a3e775d408ea6657a5332279643',
      'Content-Type': 'application/octet-stream',
    };

    final imageBytes = await imagem.readAsBytes();
    final request = http.Request('POST', url)
      ..headers.addAll(headers)
      ..bodyBytes = imageBytes;

    try {
      final streamedResponse = await request.send();
      if (streamedResponse.statusCode == 200) {
        final responseString = await streamedResponse.stream.bytesToString();
        final responseJson = json.decode(responseString);
        return responseJson;
      } else {
        throw Exception('Erro ao classificar a imagem: ${streamedResponse.reasonPhrase}');
      }
    } catch (e) {
      throw Exception('Erro ao enviar a imagem: $e');
    }
  }
}

class HistoricoService {
  static final List<Map<String, String>> _historico = [];

  static void addHistorico(Map<String, String> item) {
    _historico.add({
      ...item,
      'data': DateTime.now().toIso8601String(),
    });
  }

  static List<Map<String, String>> getHistorico() {
    return List<Map<String, String>>.from(_historico);
  }
}
