# Proxima Media Collector

비디오서버의 클립 목록을 DB에 동기화 하기 위한 서비스

## README.md

https://github.com/whamma/proxima-media-collector/tree/master

## 데이터 베이스 구축

준비사항 : PostgreSQL 9.6버전이 설치되어 있어야 한다.

아래 SQL로 테이블을 생성한다.

```
-- 비디오서버 클립 리스트
CREATE TABLE proxima_v3.videoserver_clips (
	id serial NOT NULL, --pk
	videoserver_id numeric NOT NULL, --비디오서버 아이디
	"name" varchar(260) NOT NULL, --클립명(FTP)
	filesize numeric NOT NULL, --파일크기(FTP), K2는 필드수
	last_modified timestamp NULL, --마지막 수정일시(FTP)
	sync_start_at timestamp NOT NULL, --동기화 시작일시
	created_at timestamp NOT NULL DEFAULT NOW(), --생성일시
	CONSTRAINT videoserver_clips_pk PRIMARY KEY (id)
)
WITH (
	OIDS=FALSE
) ;

COMMENT ON TABLE proxima_v3.videoserver_clips IS '비디오서버 클립리스트' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.id IS 'PK' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.videoserver_id IS '비디오서버 아이디' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.name IS '클립명(FTP)' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.filesize IS '파일크기(FTP)' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.last_modified IS '마지막 수정일시(FTP)' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.sync_start_at IS '동기화 시작일시' ;
COMMENT ON COLUMN proxima_v3.videoserver_clips.created_at IS '생성일시' ;
--비디오서버 클립 끝
```

## 서비스 구축

준비사항 : node.js 8.11.3이상이 설치되어 있어야 한다.

1. `git clone ssh://git@geminisoft.iptime.org/site-2018/huimai/media-collector.git`
2. `cd media-collector.git`
3. `yarn install 또는 npm install`
4. .env.example 파일을 .env파일로 복사 후 환경설정 진행

```
// 비디오 서버 목록을 불러오는 api endpoint
API_URL_VIDEOSERVERS=http://127.0.0.1/videoservers
// 비디오 서버 클립 정보를 등록하는 api endpoint(미구현)
API_URL_CLIPS=http://127.0.0.1/clips

// FTP 접속 타임아웃
FTP_DEFAULT_TIMEOUT=30000

// 로컬 DB 정보
DB_HOST=10.10.109.66
DB_PORT=3306
DB_DATABASE=sam
DB_USERNAME=root
DB_PASSWORD=admin
```

5. `yarn start 또는 npm start`

## 운영을 위한 주요 스크립트

업로드 서버는 pm2([http://pm2.keymetrics.io](http://pm2.keymetrics.io))를 사용하여 운영되며 주요 명령어는 package.json의 script에 미리 지정해 놓았음.

> yarn start 또는 npm start : 서비스를 시작한다.

> yarn stop 또는 npm stop : 서비스를 중지한다.

> yarn restart 또는 npm restart : 서비스를 재시작한다.

> yarn ps 또는 npm ps : 서비스 리스트를 조회한다.

> yarn logs 또는 npm logs : 서비스 로그를 조회한다.

### PM2 참고자료
- Node.js 의 프로세스 관리자인 PM2 모듈 사용하기([https://massivcode.com/5](https://massivcode.com/5))
