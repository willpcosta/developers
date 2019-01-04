Title: Webservices - ESP
Description: CITSmart - ESP Webservices

Webservices ESP -Enterprise Service Platform
==============

Este documento tem o propósito de fornecer orientações a respeito dos Web Services disponibilizados para integração com o Gerenciamento de Serviço do Citsmart ITSM.
<br>
Os Web Services foram criados no CTSmart ESP para inclusão, atualização, consulta e cancelamento de solicitações de serviço (incidentes e requisições).


##Antes de começar


1.	Antes de se utilizar qualquer operação REST do CITSmart, é necessário que o usuário esteja autenticado.
2.	A autenticação é feita através da operação REST login na URL /services/login, que recebe um objeto CtLogin contendo os atributos userName, password e platform.
3.	O atributo platform deve conter a identificação do site que está solicitando o serviço.
4.	A operação login retorna um valor alfanumérico no atributo SessionID. Este mesmo SessionID deve ser utilizado nas outras chamadas REST. O objeto retornado contém o código e descrição do erro em caso de problemas na execução da operação login.
5.	O usuário autenticado compõe a chave para sincronização dos dados, quando o atributo synchronize tiver o valor true.
6.	Os serviços de inclusão e atualização de solicitações contam com o atributo synchronize. Quando este atributo for true, o cadastro de usuário e o catálogo serviços serão automaticamente criados ou atualizados no CITSmart a partir das informações enviadas na solicitação do Web Service.  


**REGRA: todos os serviços REST criados no CITSmart recebem um objeto de entrada e retornam um objeto. Em caso de erro, o objeto de retorno contém o código e a descrição do erro. Quando não houver erro, além dos atributos definidos para cada serviço, o objeto de retorno contém a data e hora de execução e o id da operação. O CITSmart garante que toda solicitação é registrada na sua base de dados e um ID da operação é retornado para o solicitante, mesmo em caso de erro.**

##Ações


###Criação de Incidente/Requisição

!!! example "Criando uma Requisição/Incidente"
    ```tab="URL"
    /services/request/create
    ```

    ```tab="Atributos de Entrada"
    -synchronize - indica se as informações de usuário e/ou serviço serão sincronizadas.
    -sourceRequest - informações da solicitação origem da classe CtRequest, contendo:
     -numberOrigin - número da solicitação no sistema de origem (obrigatório. Este atributo é necessário para que o CITSmart mantenha o DE-PARA entre sua base de dados e o número original do sistema origem.
     -type - tipo da solicitação (obrigatório). Valores possíveis: I=Incidente ou R=Requisição.
     -description - descrição do incidente ou requisição (obrigatório).
     -userID - identificação de usuário do solicitante (obrigatório). Será incluído se não existir na base do Citsmart e o atributo synchronize for igual a true. 
     -contact - dados do solicitante. Obrigatório quando o solicitante não existir no CITSmart e o atributo synchronize for igual a true).
     -name - nome do solicitante (obrigatório).
      -phoneNumber - telefone do solicitante (obrigatório).
      -e-mail - e-mail do solicitante (obrigatório).
      -contractID - número do contrato no CITSmart (opcional). Se não for informado, o Citsmart vai incluir a solicitação vinculada ao contrato default parametrizado no serviço.
      -service - dados do serviço (opcional). Se não for informado, o Citsmart vai incluir a solicitação vinculada ao serviço default parametrizado no cadastro de WebService.
          -code - código do serviço. Opcional, se nome do serviço for informado).
          -name - nome do serviço. Obrigatório quando o serviço não existir no CITSmart e o atributo synchronize for igual a true.
          -category - categoria do serviço. Obrigatório quando o serviço não existir no CITSmart e o atributo synchronize for igual a true.
             -code - código da categoria.
             -name - nome da categoria.
      -urgency - urgência da solicitação (opcional). Valores possíveis: H=Alta, M=Média, L=Baixa. Se não for informada, a urgência será calculada partir dos parâmetros do catálogo de serviço do CITSmart.
      -impact of request (opcional). Valores possíveis: H=Alto, M=Médio, L=Baixo. Se não for informado, o impacto será calculado a partir dos parâmetros do catálogo de serviço do CITSmart.
      -groupId - sigla do grupo executor no CITSmart (opcional). Se não for informada, o grupo executor será obtido a partir dos parâmetros do catálogo de serviço do CITSmart. 
    ```

    ```tab="Atributos de Saída"
    Os atributos de saída são compostos de todos os atributos de entrada da classe CtRequest, mais as seguintes informações:
      number - número da solicitação criada no Citsmart ITSM.
      startSLA - data e hora de início do SLA.
      endSLA - data e hora de término do SLA.
      status - situação da solicitação, contendo:
         code - código da situação.
         name - nome da situação.
      currentTask - tarefa atual, contendo:
         number - número da tarefa
         name - nome da tarefa.
         startDateTime - data e hora de início
         status - situação da tarefa, contendo:
            code: código da situação.
            name: nome da situação.
      userId - login do usuário responsável pela tarefa.
      groupId - sigla do grupo responsável pela tarefa.
    ```

    ```JSON tab="Exemplo JSON"
    {"Synchronize": true, 
    "sourceRequest": {"numberOrigin": "9999", 
    "type": "R", 
    "userID": " 61 84460708 ",
    " email ":" fulano.de.tal@centralit.com.br ",
    " department ":" Department of the So-and-so ",
    " name ":" So-and-so "},
    " description ":" REST v3 ",
    " service ": {" name ":" SERVICE.TEST.1 ",
    " category ": {" name ":" Category 1 "}},
    " contractID ":" 1 ",
    " urgency " : "H", 
    "impact": "H"}
    }
    Supondo que no atributo platform no login foi informado "usuário" e considerando o atributo synchronize igual a true, o CITSmart irá:
      - Verificar se existe um DE-PARA do contrato 1 para o "usuário";
      - Incluir o solicitante no cadastro de usuários, caso não exista na base;
      - Incluir o serviço no catálogo de serviços do contrato 1, caso não exista na base e registrar o DE-PARA de serviços para o cliente;
      - Incluir a solicitação com número de origem 9999;
      - Registrar o DE-PARA da solicitação de origem 9999 para o cliente.
    ```
###Alteração de Informações de Incidente/Requisição

!!! example "Alterando informação de Requisição/Incidente"
    ```tab="URL"
    /services/request/create
    ```

    ```tab="Atributos de Entrada"
    ​​synchronize - indica se as informações de usuário e/ou serviço serão sincronizadas.
    request - informações da solicitação origem da classe CtRequest, contendo:
        numberOrigin - número da solicitação no sistema de origem. Obrigatório quando o atributo number não for informado.
        description - descrição do incidente ou requisição (opcional).
        userID - identificação de usuário do solicitante (obrigatório). Será incluído se não existir na base do CITSmart e o atributo synchronize for igual a true.
        number - número da solicitação no CITSmart (obrigatório).
        contact - dados do solicitante. Obrigatório quando o solicitante não existir no CITSmart e o atributo synchronize for igual a true).
            name - nome do solicitante (obrigatório).
            phoneNumber - telefone do solicitante (obrigatório).
            e-mail - e-mail do solicitante (obrigatório).
    ​    service - dados do serviço (opcional).
            code - código do serviço. Opcional, se nome do serviço for informado).
            name - nome do serviço. Obrigatório quando o serviço não existir no CITSmart e o atributo synchronize for igual a true.
            category - categoria do serviço. Obrigatório quando o serviço não existir no CITSmart e o atributo synchronize for igual a true.
                Code - código da categoria.
                Name - nome da categoria.
         Urgency - urgência da solicitação (opcional). Valores possíveis: H=Alta, M=Média, L=Baixa. Se não for informada, a urgência será calculada partir dos parâmetros do catálogo de serviço do CITSmart ITSM.
         impacto da solicitação (opcional). Valores possíveis: H=Alto, M=Médio, L=Baixo. Se não for informado, o impacto será calculado a partir dos parâmetros do catálogo de serviço do CITSmart ITSM.
    ```

    ```tab="Atributos de Saída"
    Os mesmos que os atributos de entrada.
    ```

    ```JSON tab="Exemplo JSON"
    {"Synchronize": true,
    "request": {"numberOrigin": "9999",
    "userID": "ciclano.de.tal",
    "contact": {"phoneNumber": "61 84460709",
    "email" "Cyclone",
    "name": "Cyclone of such"},
    "description": "Inclusion of request using REST v3 - Changed",
    "cyclano.de.tal@centralit.com.br",
    "department" "Service": {"name": "SERVICO.TESTE.2",
    "category": {"name": "Category 2"}}}}
   
    Supondo que no atributo platform no login foi informado "usuário" e considerando o atributo synchronize igual a true, o CITSmart irá:
        Incluir o solicitante no cadastro de usuários, caso não exista na base;
        Incluir o serviço no catálogo de serviços do contrato 1, caso não exista na base e registrar o DE-PARA de serviços para o cliente;
        Alterar o solicitante e serviço da solicitação com número de origem 9999.
    ```

###Alteração da situação de um Incidente/Requisição


!!! example "Alteração da situação de um Incidente/Requisição"
    ```tab="URL"
    /services/request/updateStatus
    ```

    ```tab="Atributos de entrada"
    ​​number - número da solicitação no CITSmart ITSM. Obrigatório quando o atributo numberOrigin não for informado.
    numberOrigin - número da solicitação no sistema de origem. Obrigatório quando o atributo number não for informado.
    status - situação da solicitação, contendo:
        code - código da situação (obrigatório). Valores possíveis: EmAndamento, Suspensa, Cancelada, Resolvida, Reaberta, Fechada.
        details - complemento da justificativa para alteração da situação (opcional).
    ```

    ```tab="Atributos de saída"
        Os mesmos que os atributos de entrada.
    ```

    ```JSON tab="Exemplo JSON"
       {"numberOrigin": "9999",
       "status": {"code": "Suspended",
       "details": "Integration Testing"}}
    ```

###INQUIRE THE APPLICANT's INCIDENTS AND REQUESTS

!!! example "Inquire the applicant's Incidents and Requests"
    ```tab="URL"
    /services/request/getByUser
    ```

    ```tab="Input Attributes"
    userID - applicant's user ID (required).
    description - description of the incident or request (optional).
    startDate - start date of the request (optional).
    endDate - end date of the request (optional).
    service - service data (optional).
       code - service code.
       name - name of the service.
    contractID - contract number in CITSmart (optional).
    status - request status (optional), containing:
       code - Location code. Possible values: Ending, Suspended, Canceled, Resolved, Reopened, Closed, Reclassified.
    ```

    ```tab="Output Attributes"
    Collection of objects of class CtRequest containing:
       number - request number in CITSmart.
       numberOrigin - the request number on the source system.
       type - type of request. Possible values: I = Incident or R = Request
       description - description of the incident or request.
       userID - user ID of the requester.
       urgency - urgency of the request (optional). Possible values: H = High, M = Average, L = Low. If not informed, the urgency will be calculated from the CITSmart service catalog parameters.
       impact of request (optional). Possible values: H = High, M = Medium, L = Low. If not informed, the impact will be calculated from the CITSmart service catalog parameters.
       groupId - execution group acronym in CITSmart (optional). If not informed, the executor group will be obtained from the CITSmart service catalog parameters.
       startDateTime - the start date and time of the request.
       startSLA - SLA start date and time.
       endSLA - end date and time of the SLA.
       status - status of the request, containing:
          code - code of the situation.
          name -name of the situation.
    ```

    ```JSON tab="JSON Example"
    {"userID": "john elliot ",
    "startDate": "2015-09-16T03:00:00.000Z",
    "endDate": "2015-09-19T03:00:00.000Z"}
    ```
###INCIDENT DETAIL / REQUEST OF APPLICANT

!!! example "Details of The Request/Incident"
    ```tab="URL"
    /services/request/getById
    ```

    ```tab="Input Attributes"
    number - request number in CITSmart. Required when the numberOrigin attribute is not informed.
    numberOrigin - request number in the source system. Required when the number attribute is not entered.
    ```

    ```tab="Output Attributes"
    Output Attributes are composed of all Input Attributes of the CtRequest class plus the following information:
      number - number of the request created in CITSmart.
      startSLA - SLA start date and time.
      endSLA - SLA end date and time.
      status - status of the request, containing:
         code - code of the situation.
         name - name of the situation.
      currentTask - current task, containing:
         number - task number
         name - name of the task.
         startDateTime - start date and time
         status - task status, containing:
            code: code of the situation.
            name: name of the situation.
      userId - login of the user responsible for the task.
      groupId - acronym of the group responsible for the task.
    ```

    ```JSON tab="JSON Example"
    {"numberOrigin":"9999"}
    ```


###INCLUDES OCCURENCE ON REQUEST
    
!!! example "Include an occourrence on a Request"
    ```tab="URL"
    /services/request/createOccurrence
    ```
    
    ```tab="Input Attributes"
    requestNumber - request number in CITSmart. Required when the requestNumberOrigin attribute is not informed.
    requestNumberOrigin - request number in the source system. Required when the requestNumber attribute is not informed.
    ocurrence - object of class CtOccurrence, containing:
        numberOrigin - occurrence number in the source system (optional).
        description - occurrence description.
        date - date of record of occurrence.
        hour - time to record the occurrence in the format HH: MM.
        category - category of occurrence. Possible values: Monitoring, Update, Diagnostics, Investigation, Memo, Information, Return, Symptom, Outline, Scheduling.
        reason - reason for occurrence.
    ```
    
    ```tab="Output Attributes"
    Object of class CtOcurrence containing:
        number - event number in CITSmart.
        numberOrigin - occurrence number in the source system.
        description - occurrence description.
        date - date of record of occurrence.
        Hour - time to record the occurrence in the format HH: MM.
        userID - identification of the user responsible for recording the occurrence.
        origin - origin of occurrence. Possible values: EMAIL, FONE_FAX, VOICE_MAIL, PERSONALLY, OTHERS.
        category - category of occurrence. Possible Values: Creation, Monitoring, Update, Diagnosis, Investigation, Memo, Information, Return, Symptom, Outline, Executing, Exchanging, Reclaiming, Reclassification, Schedule, Suspend, Reopen, Targeting, Sharing, Cancellation Task, HomeSLA, SuspendedSLA, Approval, ReactivationSLA
        elapsedTime - elapsed time (for Category of Executing type)
        reason – reason for occurrence.
        task - the task associated with the occurrence, containing:
           number - task number.
           name - name of the task.
           startDateTime - start date and time.
           endDateTime - date and time of execution.
           status - status of the task, containing:
              code - location code.
              name - name of the situation.
          userId - login of the user responsible for the execution of the task.
    ```
    
    ```JSON tab="JSON Example"
        {"requestNumberOrigin": "9999",
        "occurrence": {"description": "Occurrence test","category": {"code": "Workaround solution"},
        "date": "2015-08-20T03:00:00.000Z",
        "hour": "2219"}}
    ```

###QUERY REQUEST OCCURENCES

!!! example "Querying Information from Requests/Incidents"
    ```tab="URL"
    /services/request/listOccurrences
    ```

    ```tab="Input Attributes"
    requestNumber - request number in CITSmart. Required when the requestNumberOrigin attribute is not informed.
    requestNumberOrigin - request number in the source system. Required when the requestNumber attribute is not informed.
    ```

    ```tab="Output Attributes"
    Object of class CtOcurrence containing:
        number - event number in CITSmart.
        numberOrigin - occurrence number in the source system.
        description - occurrence description.
        date - date of record of occurrence.
        Hour - time to record the occurrence in the format HH: MM.
        userID - identification of the user responsible for recording the occurrence.
        origin - origin of occurrence. Possible values: EMAIL, FONE_FAX, VOICE_MAIL, PERSONALLY, OTHERS.
        category - category of occurrence. Possible Values: Creation, Monitoring, Update, Diagnosis, Investigation, Memo, Information, Return, Symptom, Outline, Executing, Exchanging, Reclaiming, Reclassification, Schedule, Suspend, Reopen, Targeting, Sharing, Cancellation Task, HomeSLA, SuspendedSLA, Approval, ReactivationSLA
        elapsedTime - elapsed time (for Category of Executing type)
        reason – reason for occurrence.
        task - the task associated with the occurrence, containing:
           number - task number.
           name - name of the task.
           startDateTime - start date and time.
           endDateTime - date and time of execution.
           status - status of the task, containing:
              code - location code.
              name - name of the situation.
          userId - login of the user responsible for the execution of the task.
    ```

    ```JSON tab="JSON Example"
    {"requestNumberOrigin":"9999"}
    ```

<hr>
<font  Size=2><b>Produto/Versão:</b> CITSmart ESP | 8.00</font> &nbsp; &nbsp;
<font  Size=2><b>Atualização:</b>12/12/2018 - Andre Luiz de Oliveira Fernandes</font>
	






