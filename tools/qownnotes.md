---
description: Essa página é a respeito do QOwnNotes que serve de suporte para anotações
---

# QOwnNotes

```
└─$ cat absolute-media-links.qml                         
import QtQml 2.0

/**
 * This script will make the paths of inserted media files absolute in the markdown link (tested on Linux)
 */
QtObject {
    /**
     * This function is called when media file is inserted into the note
     * 
     * @param fileName string the file path of the source media file before it was copied to the media folder
     * @param mediaMarkdownText string the markdown text of the media file, e.g. ![my-image](file://media/505671508.jpg)
     * @return string the new markdown text of the media file
     */
    function insertMediaHook(fileName, mediaMarkdownText) {
        //Obtem o nome relativo do arquivo (sem ter o caminho completo)
        var relativeFileName = mediaMarkdownText.split("(")[1].split(")")[0];
        script.log(relativeFileName);

        //Obtendo o nome do arquivo gerado da midia que colocamos na nota...
        var array = relativeFileName.split("/");
        var file_name = array[array.length - 1];

        //Ajusta o caminho completo do arquivo no diretorio de midia original do QOwnNotes
        var full_path_file_temp = "/media/owncloud/Notes/media/" + file_name;

        //ALTERAR ESTE CAMINHO caso ocorra a mudança de diretório do github assets
        var gitbook_assets = "/media/owncloud/Notes/InfraHacking/.gitbook/assets";

        //copiando o arquivo de midia para o diretorio do gitbook assets
        script.startDetachedProcess("/usr/bin/cp", [full_path_file_temp, gitbook_assets]);

        //Obtendo o nome do arquivo com o formato "![qownnotes-media-VMgxgZ]", por exemplo
        var name_inside_brackets = mediaMarkdownText.split("(")[0];

        //Gerando o caminho completo do arquivo depois de transferido para o diretorio do gitbook
        var full_path_filename = gitbook_assets + "/" + file_name;

        //Ajustando a string para ser colocada na nota e obter a referencia correta da midia...
        //Lembrando que, na configuracao atual dos diretorios aqui, temos que levar em conta que o gitbook esta um diretorio DEPOIS do /media/ e , por isso 
        //eh necessario substituir o "../"
        var m_temp = name_inside_brackets + "(" + relativeFileName.replace("../media/", ".gitbook/assets/") + ")"; 
        mediaMarkdownText = m_temp;

        return mediaMarkdownText;
    }
}

```

Eu desenvolvi esse script para ajustar e adequar as imagens que são aramazenadas locamente na máquina, mas que para haver sincronia com o github e gitbook se faz necessário.
