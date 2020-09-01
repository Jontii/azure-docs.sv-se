---
title: Självstudie `:` Använd en hanterad identitet för att få åtkomst till Azure Key Vault – Linux – Azure AD
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6756d66f176314ad5abd0c94f7bdf96c42a460a4
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255317"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att komma åt Azure Key Vault. Key Vault fungerar som en bootstrap som gör det möjligt för klientprogrammet att använda hemligheten för åtkomst till resurser som inte skyddas av Azure Active Directory (AD). Hanterade identiteter för Azure-resurser hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. 

Lär dig att:

> [!div class="checklist"]
> * Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault 
> * Få ett åtkomsttoken med hjälp av identiteten för de virtuella datorerna och använd den för att hämta hemligheten från Key Vault 
 
## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault  

Med hjälp av hanterade tjänstidentiteter för Azure-resurser kan din kod hämta åtkomsttoken och autentisera mot resurser som stöder Azure Active Directory-autentisering.Men alla Azure-tjänster stöder inte Azure Active Directory-autentisering.Om du vill använda hanterade identiteter för Azure-resurser med dessa tjänster lagrar du autentiseringsuppgifterna för tjänsten i Azure Key Vault och får åtkomst till Key Vault och hämtar autentiseringsuppgifterna med hjälp av hanterade identiteter för Azure-resurser. 

Börja med att skapa ett Key Vault och bevilja den virtuella datorns systemtilldelade hanterade identitet åtkomst till Key Vault.   

1. Längst upp i det vänstra navigerings fältet väljer du **skapa en resurs**  >  **säkerhet och identitet**  >  **Key Vault**.  
2. Ange ett **namn** för det nya Key Vault. 
3. Leta upp Key Vault i samma prenumerations- och resursgrupp som den virtuella dator du skapade tidigare. 
4. Välj **Åtkomstprinciper** och klicka på **Lägg till**. 
5. I Konfigurera från mall väljer du **Hemlighetshantering**. 
6. Välj **Välj huvudkonto** och ange namnet på den virtuella dator som du skapade tidigare i sökfältet.Välj den virtuella datorn i resultatlistan och klicka på **Välj**. 
7. Klicka på **OK** och lägg till den nya åtkomstprincipen. Klicka sedan på **OK** och slutför valet av åtkomstprincip. 
8. Klicka på **Skapa** och skapa Key Vault. 

    ![Alternativ bildtext](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Lägg sedan till en hemlighet i Key Vault, så att du senare kan hämta hemligheten med hjälp av koden som körs i den virtuella datorn: 

1. Välj **Alla resurser** och leta upp och välj det Key Vault som du skapade. 
2. Välj **Hemligheter** och klicka på **Lägg till**. 
3. Välj **Manuell** från **Uppladdningsalternativ**. 
4. Ange ett namn och värde för hemligheten.Värdet kan vara vad du vill. 
5. Låt aktiveringsdatum och förfallodatum vara tomt och sätt **Aktiverad** som **Ja**. 
6. Klicka på **Skapa** och skapa hemligheten. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Få ett åtkomsttoken med hjälp av identiteten för de virtuella datorerna och använd den för att hämta hemligheten från Key Vault  

För att slutföra de här stegen behöver du en SSH-klient.Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](/windows/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).
 
1. I portalen går du till den virtuella Linux-datorn och i **översikten** klickar du på **Anslut**. 
2. **Anslut** till den virtuella datorn med valfri SSH-klient. 
3. Använd CURL i terminalfönstret och skicka en begäran till de lokala hanterade identiteterna för Azure-resursslutpunkter för att hämta en åtkomsttoken för Azure Key Vault.  
 
    CURL-begäran för åtkomsttoken visas nedan.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Svaret innehåller den åtkomsttoken som du behöver för att komma åt Resource Manager. 
    
    Svar:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Du kan använda denna åtkomsttoken för att autentisera till Azure Key Vault.Nästa CURL-begäran visar hur du kan läsa en hemlighet från Key Vault med hjälp av CURL och Key Vault REST API:et.Du behöver URL:en till ditt Key Vault, som finns i avsnittet **Essentials** på Key Vault-sidan **Översikt**.Du Behöver också den åtkomsttoken som du fick i det föregående anropet. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Svaret ser ut såhär: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Key Vault kan du använda den och autentisera mot en tjänst som kräver ett namn och lösenord.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Key Vault.  Läs mer om Azure Key Vault här:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)