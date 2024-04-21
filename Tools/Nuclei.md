Nuclei
========================


Nuclei é um scan de vulnerabilidades altamente customizável...

    https://nuclei.projectdiscovery.io/nuclei/get-started/

Os templates para execução dos scans são salvos em uma pasta chamada nuclei-teamplates e pode ser atualizada por meio do seguinte comando:
    
    mkdir ~/nuclei-templates
    nuclei -ut
    
Forma mais básica de utilizar o nuclei:

    nuclei -u https://vivo.lightning.force.com
    
Como eu quis utilizar um template específico informei isto no seguinte parâmetro:

    nuclei -u https://vivo.lightning.force.com -t misconfiguration/
    
    -H # esse parâmetro nos permite colocar um cookie no cabeçalhjo, dando condições de realizar scans autenticados
    
    