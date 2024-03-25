# Google Summer of Code 2024

### Project Proposal - Agora Blockchain

### Personal Details

Name: Aditya Bhattad

Email: adityabhattad18@gmail.com

College Name: [Shri Ramdeobaba College of Engineering and Management](http://www.rknec.edu/)

Degree: Bachelor of Technology in Computer Science and Engineering

Expected Graduation Year: 2025

# Chosen Idea: Agora Blockchain

## 1.Introduction

**1.1 Project Overview**

The Agora Blockchain DApp is a decentralized voting application that enables secure, transparent and anonymous voting processes using zero knowledge proofs and blockchain. It offers features such as user registration, election creation, voting, result calculation, user KYC using Polygon ID, and an anonymous voting flow using the Semaphore library.

**1.2 Current State of Agora Blockchain DApp**

While the Agora Blockchain DApp provides a solid foundation for conducting elections on the blockchain, there are several areas that can be enhanced to improve user experience, accessibility, and overall functionality. The current implementation lacks sufficient abstraction of blockchain complexities for non-technical users, and the codebase requires refactoring and organization to facilitate easier development and maintenance.

**1.3 Proposed Enhancements**

This proposal aims to enhance the current Election DApp by adding new features and refactoring existing ones.
Here is a summary of the proposed improvements:

1. **Abstraction of Blockchain**: The goal is to partially abstract the blockchain part from the election administrator and fully abstract it from the voter. This will allow users to enjoy the benefits of blockchain technology, such as immutability, without having to worry about their wallet address or blockchain transactions. The user experience will be native and completely anonymous and secure.
2. **Election Subscription**: Allow voters to subscribe to election results and receive notifications when elections conclude, keeping them engaged and informed.
3. **Code Refactoring**: Rewrite the Client Side Code in `Nextjs`/ `Typescript` and Organize the Codebase: The current repository is divided into several different folders, such as client, server, `clientAnonymous`, and `serverAnonymous`. Some libraries used in the client, like `rimble-ui`, `libsemaphore`, and `react16`, are long deprecated and not actively maintained anymore. This leads to a poor developer experience and slows down development due to conflicts. Rewriting the client and organizing the codebase properly can solve these issues.
4. **Connect Anonymous Voting to Multiple Voting Algorithms and Extensively Test Smart Contracts**: Currently, anonymous voting only supports normal general election types. It needs to be integrated with other smart contracts that have implemented multiple election algorithms using the Diamond Multi-Facet Proxy. To gain the trust of users and avoid bugs during usage, an extensive test suite that tests every aspect of the smart contract is required. This will make the smart contract much more reliable.
5. **Add More Election Types**: There is still room to add more election types, such as approval voting and quadratic voting, to give users more choices.

By implementing these enhancements, the Election D-App will become more user-friendly, secure, and accessible to a wider audience. The proposed changes will significantly improve the user experience, increase trust in the platform, and contribute to the overall adoption of the project.

## Detailed Proposal Description

## 1. Abstraction of Blockchain

- **1.1 Motivation and Goals**
    
    In 2024, abstraction plays a crucial role in the adoption of blockchain-powered applications. Users should be able to benefit from immutability and security without needing to understand the underlying blockchain technology. 
    
    The goals of this part of the project are:
    
    1. Allow general users to register and vote without interacting with blockchain wallets
    2. Partially abstract blockchain details for election admins while still giving them control
    3. Provide a seamless, web2-like user experience while leveraging web3 benefits under the hood
- **1.2 User Flows**
    - **1.2.1 Onboarding Flow for General Users**
        1. User registers to the application using a standard web2 flow like sign in with Google or email. User details are saved to the backend.
        2. Backend generates a random passphrase string and maps it to the user account. This will later be used to create the identity commitment for the user.
        3. User is acknowledged and redirected to the general dashboard of the voting application.
            
            ![user registration.png](assets/user_registration.png)
            
    - **1.2.2 Election Admin Flow**
        1. Registered users can choose to create an election. When they click on create election button, they are prompted to connect their web3 wallet in order make a smart contract call to create election on blockchain, which is necessary for the election admin to have independent control, making our application truly decentralized.
        To make our application more accessible we can enable functionality that will allow the user to create wallet on fly using their connected email addresses.
        2. Once the wallet is connected and linked to their account, the election admin will:
            1. Choose the election type
            2. Add the election title and description
            3. Add candidates
            4. Set the start time and duration for the election
            5. And click submit
        3. Election details are sent to the entry point smart contract, from where a new proxy contract is deployed which will be linked to the logic contracts (Ballot and Result Calculators) of the selected algorithm.
        4. Upon successful blockchain transaction, backend is updated with election details metadata and admin is redirected to admin dashboard from where they can manage the election and approve voters.
        
        ![election admin flow.png](assets/election_admin_flow.png)
        
    - **1.2.3 Voter Flow**
        1. Onboarded users can browse and search available elections.
        2. To vote in the particular election, users first have to submit application for approval from the admin on their election page.
        3. Users wait for approval from the election admin.
        4. When an admin approves the user, their email address along with a secret key on the backend, is used to generate an Identity Commitment, which will be stored on the smart contract of the election they have been approved for.
        5. Voters are notified via email once approved.
        6. After approval, when the voting period starts, the user can submit their vote:
            1. When user clicks on vote, their Identity Commitment is again generated on the backend using their email and secret key, which then fetched to the client to generate ZK proofs.
            2. Using the users identity commitment and the election Id the ZK proofs are generated on the client.
            3. Proofs are submitted to backend which works as a re-layer in this case, from here the proofs are submitted to the blockchain in a transaction.
            4. Blockchain verifies the ZK proofs to confirm the user is an approved voter who hasn't yet voted. If valid, their vote is registered.
        7. Users can subscribe to an election to be notified of the results via email once it concludes.
        
        ![voter flow.png](assets/voter_flow.png)
        
    
- 1.3 Technical Implementation
    
    Assuming we are rewriting the client in Next.js, here is an overview of the technical implementation:
    
    - Use Next.js to create user interfaces for registration, election creation/management, and voting.
    - Integrate Web3Modal library for seamless web3 wallet connection and on-the-fly wallet creation for election admins.
    - Use Next.js API routes to handle backend logic like user registration, identity commitment generation and working as a middle layer to send the ZK Proofs to the blockchain.
    - Utilize Semaphore SDK to develop anonymous voting flow
    - Use Supabase PostgreSQL to store user info and election metadata.
    - Utilize NextAuth to handle user authentication in the application.
    - 1.3.1 Architecture
        
        ![System Architecture Diagram.png](assets/System_Architecture_Diagram.png)
        
    - 1.3.2 Frontend Implementation in Next.js/TypeScript
        - 1.3.2.1 Screens
            
            Frontend will have the following screens
            
            - **Agora Blockchain Landing Page (Not protected)**: This page can be used to explain how the entire anonymous flow works, and a brief overview about all the available algorithms, this will help the election organizer to make an informed choice while election algorithm selection.
            - **Login/Registration Page (Not protected)**: We can conditionally render login and register input fields and an separate option to sign in with google.
            - **General Dashboard (Not protected)**: This page will contains stats about all the election like created, on going, concluded, etc. and list of all the elections in table along with a search bar to search for the relevant election.
            - **Election Creation Modal (Protected)**: The modal will contain input field for election title, election description, election type, and start date and duration of the election.
            - **Election Admin dashboard (Protected)**: This will contain list of all the elections created by the users, list of all the voters applied for the election, a button to add candidates for the election, and a button to conclude the election.
                
                ![Agora Blockchain Election Dashboard.png](assets/Agora_Blockchain_Election_Dashboard.png)
                
            - **Add candidate Modal (Protected)**: This will contain input fields for name, description and image of the candidate.
            - **Election Page (Protected)**: This page will contain the election details like start time, end time, number of voters registered for the election, candidates contesting in the elections, etc.
            - **Vote Modal (Protected)**: This will contain input field to choose candidate from the all the candidates to vote in the elections, we will have to conditionally render multiple types of UIs here based on the election algorithm.
        - 1.3.2.2 User Authentication with NextAuth.js (Backend side of the logic is discussed separately).
            1. After setting up the NextAuth.Js on backend we will have to implement a middleware that will block the access to the protected pages of our application.
                
                This can be implemented by adding `middleware.ts` file in the root of our NextJs Application.
                
                ```tsx
                import NextAuth from "next-auth";
                import authConfig from "./auth.config";  // this will be covered in the backend part.
                import { DEFAULT_LOGIN_REDIRECT, publicRoutes } from "./routes";
                
                const { auth } = NextAuth(authConfig);
                
                const apiAuthPrefix = "/api/auth";
                
                const publicRoutes = [
                    "/",
                    "/auth/verify-email",
                ];
                
                const authRoutes = [
                    "/auth/login",
                    "/auth/register",
                    "/auth/error",
                ];
                
                const DEFAULT_LOGIN_REDIRECT = "/dashboard";
                
                export default auth((req) => {
                    const { nextUrl } = req;
                    const isLoggedIn = !!req.auth;
                
                    const isApiAuthRoute = nextUrl.pathname.startsWith(apiAuthPrefix);
                    const isPublicRoute = publicRoutes.includes(nextUrl.pathname);
                    const isAuthRoute = authRoutes.includes(nextUrl.pathname);
                
                    if (isApiAuthRoute) {
                        return null;
                    }
                
                    if (isAuthRoute) {
                        if (isLoggedIn) {
                            return Response.redirect(new URL(DEFAULT_LOGIN_REDIRECT, nextUrl));
                        }
                        return null;
                    }
                
                    if (!isLoggedIn && !isPublicRoute) {
                        let callBackUrl = nextUrl.pathname;
                        if (nextUrl.search) {
                            callBackUrl += nextUrl.search;
                        }
                
                        const encodedCallbackUrl = encodeURIComponent(callBackUrl);
                        return Response.redirect(new URL(`/auth/login?callbackUrl=${encodedCallbackUrl}`, nextUrl));
                    }
                
                    return null;
                })
                
                export const config = {
                    matcher: ["/((?!api|_next/static|_next/image|favicon.ico).*)"],
                }
                ```
                
            2. To be able use/display users information throughout the client, we will need to use the `useSession` hook, first we have to expose the session context, **`<SessionProvider />`**, at the top level of our application:
                
                ```tsx
                export default async function RootLayout({
                  children,
                }: Readonly<{
                  children: React.ReactNode;
                }>) {
                  const session = await auth();
                  return (
                    <SessionProvider session={session}>
                      <html lang="en">
                        <body className={inter.className}>{children}</body>
                      </html>
                    </SessionProvider>
                  );
                }
                ```
                
            3. Then we can use the user information in any component like:
                
                ```tsx
                import { useSession } from "next-auth/react"
                export default function Component() {
                  const { data: session } = useSession()
                    return (
                      <>
                        Signed in as {session.user.email}
                      </>
                  }
                }
                ```
                
        
        - 1.3.2.3: Add functionality to connect wallet for the election organizer: To streamline the development of the connect wallet flow and make wallet connection experience smoother by adding functionality to generate a wallet on fly using email address, we can use walletconnect’s `web3modal` .
            
            To set it up we have to:
            
            1. Create a new file `context/web3modal.tsx` outside the app directory, this file will include all the configuration required for web3 modal.
                
                ```tsx
                'use client'
                
                import { createWeb3Modal, defaultConfig } from '@web3modal/ethers5/react'
                
                const projectId = process.env.WALLET_CONNECT_PROJECT_ID
                
                // example chain
                const mainnet = {
                  chainId: 1,
                  name: 'Ethereum',
                  currency: 'ETH',
                  explorerUrl: 'https://etherscan.io',
                  rpcUrl: 'https://cloudflare-eth.com'
                }
                
                // 3. Create a metadata object
                const metadata = {
                  name: 'Agora Blockchain',
                  description: 'Agora is a blockchain-based voting system that allows for secure, anonymous, and transparent voting',
                  url: 'https://agorablockchain.com', // origin must match your domain & subdomain
                  icons: ['https://icon.agorablockchain.com/']
                }
                
                // 4. Create Ethers config
                const ethersConfig = defaultConfig({
                  metadata,
                	enableEmail: true // This well enable option to create wallet with email addresses
                })
                
                // 5. Create a Web3Modal instance
                createWeb3Modal({
                  ethersConfig,
                  chains: [mainnet],
                  projectId,
                })
                
                export function Web3Modal({ children }) {
                  return children
                }
                ```
                
            2. To make `useWeb3Modal` hook available in our entire application we will have to  expose this **`<Web3Modal />`** context, at the top level of our application:
                
                ```tsx
                import './globals.css'
                
                import { Web3Modal } from '../context/web3modal'
                
                export const metadata = {
                  title: 'Web3Modal',
                  description: 'Web3Modal Example'
                }
                
                export default function RootLayout({ children }) {
                  return (
                  <SessionProvider session={session}>
                      <html lang="en">
                        <body className={inter.className}>
                		       <Web3Modal>{children}</Web3Modal>
                        </body>
                      </html>
                    </SessionProvider>
                  )
                }
                ```
                
            3. Then we can simple use `useWeb3Modal` hook to trigger the wallet connection modal
                
                ```tsx
                import { useWeb3Modal } from '@web3modal/ethers/react'
                
                export default function ConnectButton() {
                  // 4. Use modal hook
                  const { open } = useWeb3Modal()
                
                  return (
                    <>
                      <button onClick={() => open()}>ConnectWallet</button>
                    </>
                  )
                }
                ```
                
                On clicking `Connect Wallet`, a modal like this will show up, allowing the user to connect their browser wallet or create a wallet with their emails.
                
                ![Connect Wallet.png](assets/Connect_Wallet.png)
                
        - 1.3.2.4: Using semaphore SDK to generate zero knowledge proofs on the frontend:
            
            What is Semaphore?
            
            [Semaphore](https://github.com/semaphore-protocol/semaphore/tree/v3.15.2) is a [zero-knowledge](https://z.cash/technology/zksnarks) protocol that allows users to cast a signal (In user case a vote) as a provable group member (i.e. voter approved by the election admin) without revealing users identity. Additionally, it provides a simple mechanism to prevent double-signaling (casting vote more than ones). 
            
            It consists of three main parts
            
            - Semaphore groups: A Semaphore group is an [incremental Merkle tree](https://docs.semaphore.pse.dev/V3/glossary#incremental-merkle-tree), and group members are tree leaves. Semaphore groups set the following three parameters:
                - **Group id**: a unique identifier for the group
                - **Tree depth**: the maximum number of members a group can contain (`max size = 2 ^ tree depth`)
                - **Members**: the list of members to initialize the group.
            - Semaphore identities: In order to join a semaphore group, a user must first create a semaphore identity.
                
                Identity contains two random secret values: `trapdoor` and `nullifier`, and one public value: `commitment`.
                
                <aside>
                💡 Poseidon is a specialized hash function co-designed with zk-proof systems in mind, enabling practical hashing of field elements inside zk-friendly arithmetic circuits with attractive performance and security properties.
                
                </aside>
                
                The Poseidon hash of the identity nullifier and trapdoor is called the *identity secret*, and its hash is the *identity commitment*.
                
                An identity commitment, similarly to Ethereum addresses, is a public value used in Semaphore groups to represent the identity of a group member. The secret values are similar to Ethereum private keys and are used to generate Semaphore zero-knowledge proofs and authenticate signals.
                
            - Semaphore proofs: Once a user joins their Semaphore identity to a Semaphore group, the user can signal anonymously with a zero-knowledge proof that proves the following:
                - the user is a member of the group,
                - the same user created the signal and the proof.
            
            We can use these three in combination to implement our anonymous voting flow:
            
            1. When the voter applies for the an election, and is approved by the admin an deterministic identity is created for the user using combination of their verified `email` and a `secret-key` stored on the backend. 
            2. The identity commitment (i.e. Public Value) is added to the on-chain group that corresponding to an particular election.
            3. Then, when a approved user clicks on cast vote on the client:
                - The group corresponding to the election is fetched from the blockchain.
                    
                    ```tsx
                    import { Group } from "@semaphore-protocol/group"
                    import { SemaphoreEthers } from "@semaphore-protocol/data"
                    
                    function getGroupId(electionId:number):Group{
                    		const groupId = electionId
                    		const semaphoreEthers = new SemaphoreEthers(process.env.NEXT_PUBLIC_RPC_URL)
                    		const members = await semaphoreEthers.getGroupMembers(groupId)
                    		const group = new Group(groupId, members);
                    		return group;
                    }
                    ```
                    
                - The identity is re-generated on the using the server actions and identity commitment is retrieved on the client.
                    
                    ```tsx
                    // Generate identity commitment using secret key and email
                    "use server"
                    import crypto from 'crypto';
                    import { Identity } from "@semaphore-protocol/identity"
                    
                    export function getUserIdentityCommitment(userEmail: string): string {
                      const hash = crypto.createHash('sha256');
                      
                      const length:number = 32;
                      const seed = userEmail + process.env.SECRET_KEY;
                      hash.update(seed);
                      const buffer = crypto.randomBytes(length);
                      const charCodes = new Array(length);
                      for (let i = 0; i < length; i++) {
                        const hashByte = hash.digest()[0];
                        const combinedByte = hashByte ^ buffer[i];
                        const charCode = 32 + (combinedByte % 95);
                        charCodes[i] = charCode;
                        hash.update(String.fromCharCode(charCode));
                      }
                      const hashStr = String.fromCharCode(...charCodes);
                      const identity = new Identity(hasStr)
                    	return {"identityCommitment":identity.commitment};
                    }
                    ```
                    
                    ```tsx
                    // Get the identity commitment on the client
                    let identityCommitment;
                    getUserIdentityCommitment(session.user.email).then((data) => {
                           identityCommitment = data.identityCommitment;
                     }).catch((error) => {
                           setError("Something went wrong!");
                           console.log('[VOTE_MODAL]: ', error);
                    });
                    ```
                    
                - ZK Proofs are generated which can prove that user is approved by the election admin and is the same user who created the proof.
                    
                    ```tsx
                    import { generateProof } from "@semaphore-protocol/proof";
                    
                    const { proof, merkleTreeRoot, nullifierHash } = await generateProof(
                             identity,
                             group,
                             electionId.toNumber(),
                             vote
                     );
                    ```
                    
                - The ZK Proofs along with the user vote, are then passed to the backend, from where they are then submitted to the blockchain, to be verified and then their vote is registered.
                    
                    <aside>
                    💡 External nullifier is used to prevent user from voting twice in the same election.
                    
                    </aside>
                    
                    ```tsx
                    let response;
                    response = await fetch("api/vote", {
                            method: "POST",
                            headers: { "Content-Type": "application/json" },
                            body: JSON.stringify({
                                  vote,
                                  merkleTreeRoot,
                                  nullifierHash,
                                  proof,
                                  // This will work as external nullifier.
                                  electionId
                    	      })
                     })
                    ```
                    
    - 1.3.3 Backend Implementation
        - 1.3.3.1: Setup database with Prisma as ORM.
            - Database design: The database schema consists of four main tables: Users, Elections, Candidates, and Voter_Registrations.
                - The Users table stores information about all of our application's users, it includes their unique user_id, email, user_type (normal or election_creator), and wallet_address. The user_type field differentiates between regular users who can register to vote and users who can create elections. The wallet_address is used to connect the user's web3 wallet for creating elections.
                - The Elections table contains details about each election, such as its unique election_id, creator_id (referencing the Users table), contract_address of the associated smart contract, title, description,type, start_time, end_time, status, subscribed users and the election result.
                - The Candidates table stores information about the candidates for each election. It includes a unique candidate_id, the election_id it belongs to (referencing the Elections table), the candidate's name, additional information, a photo_url.
                - The Voter_Registrations table manages the voter registration process. It contains a unique registration_id, the election_id (referencing the Elections table), the user_id of the voter (referencing the Users table), and the status of their registration (pending, approved, or rejected.
            - Setting up Prisma as ORM:
                
                Autogenerated `prisma/**schema.prisma` file:**
                
                ```tsx
                datasource db {
                  provider = "postgresql"
                  url      = env("DATABASE_URL")
                }
                
                generator client {
                  provider = "prisma-client-js"
                }
                
                model User {
                  user_id        String            @id @default(cuid())
                  email          String            @unique
                  user_type      String
                  wallet_address String?
                  registrations  Voter_Registration[]
                }
                
                model Election {
                  election_id      String        @id @default(cuid())
                  creator_id       String
                  contract_address String
                  title            String
                  description      String
                  electionType     String
                  start_time       DateTime
                  end_time         DateTime
                  status           String
                  result           String?  // we will store candidate_id here.
                  candidates       Candidate[]
                  registrations    Voter_Registration[]
                  subscribed_users User[]
                  candidate  Candidate @relation(fields: [result],references: [candidate_id]
                }
                
                model Candidate {
                  candidate_id String       @id @default(cuid())
                  election_id  String
                  name         String
                  info         String?
                  photo_url    String?
                  election     Elections @relation(fields: [election_id], references: [election_id])
                }
                
                model Voter_Registration {
                  registration_id String       @id @default(cuid())
                  election_id     String
                  user_id         String
                  status          String
                  election        Elections @relation(fields: [election_id], references: [election_id])
                  user            Users     @relation(fields: [user_id], references: [user_id])
                }
                
                // ############ Tables below are required for next auth ###############
                model Account {
                  id                String  @id @default(cuid())
                  userId            String
                  type              String
                  provider          String
                  providerAccountId String
                  refresh_token     String? @db.Text
                  access_token      String? @db.Text
                  expires_at        Int?
                  token_type        String?
                  scope             String?
                  id_token          String? @db.Text
                  session_state     String?
                
                  user User @relation(fields: [userId], references: [user_id], onDelete: Cascade)
                }
                
                model VerificationToken {
                  id      String   @id @default(cuid())
                  email   String
                  token   String   @unique
                  expires DateTime
                
                  @@unique([email, token])
                }
                ```
                
                To set up Prisma client we need to create new file `lib/prisma.ts` as follows:
                
                ```tsx
                import { PrismaClient } from '@prisma/client';
                
                const globalForPrisma = global as unknown as { prisma: PrismaClient };
                
                export const prisma =
                  globalForPrisma.prisma ||
                  new PrismaClient({
                    log: ['query'],
                  });
                
                if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
                ```
                
                This will allow us to easily query the database from our API routes and server components when needed.
                
        - 1.3.3.2: Setup NextAuth.Js
            
            As we provide our users with two options to login/register: 
            
            1. Sign In with Google
            2. Email and Password
            - We will have to create two files in the root of our project
                1. auth.config.ts
                    
                    ```tsx
                    // We have this file so that we can use edge, even though prisma doesn't works with edge.
                    import Credentials from "next-auth/providers/credentials";
                    import Google from "next-auth/providers/google";
                    
                    import type { NextAuthConfig } from "next-auth";
                    import { LoginSchema } from "./form-schema";
                    import { getUserByEmail } from "./utils/user";
                    import bcrypt from "bcryptjs";
                    
                    export default {
                        providers: [
                            Google({
                                clientId: process.env.GOOGLE_CLIENT_ID,
                                clientSecret: process.env.GOOGLE_CLIENT_SECRET,
                            }),
                            Credentials({
                                async authorize(credentials) {
                    
                                    const validatedFields = LoginSchema.safeParse(credentials);
                    
                                    if (validatedFields.success) {
                                        const { email, password } = validatedFields.data;
                    
                                        const user = await getUserByEmail(email);
                                        if (!user || !user.password) {
                                            return null;
                                        }
                    
                                        const isPasswordCorrect = await bcrypt.compare(
                                            password,
                                            user.password
                                        );
                    
                                        if (isPasswordCorrect) {
                                            return user;
                                        }
                                    }
                                    return null;
                                }
                            })
                        ],
                    } satisfies NextAuthConfig;
                    ```
                    
                2. auth.ts
                    
                    ```tsx
                    import NextAuth, { type DefaultSession, type User } from "next-auth";
                    import { PrismaAdapter } from "@auth/prisma-adapter";
                    import { db } from "./lib/db";
                    import authConfig from "./auth.config";
                    import { getUserById } from "./utils/user";
                    import { getTwoFactorConfirmationByUserId } from "./utils/two-factor-confirmation";
                    import { getAccountByUserId } from "./actions/account";
                    
                    export type ExtendedUser = DefaultSession["user"] & {
                        role: string;
                        isOAuth: boolean;
                    }
                    
                    declare module "next-auth" {
                        interface Session {
                            user: ExtendedUser;
                        }
                    }
                    
                    export const {
                        handlers: { GET, POST },
                        auth,
                        signIn,
                        signOut
                    } = NextAuth({
                        // So that it automatically marks email verified on OAuth
                        pages: {
                            signIn: "/auth/login",
                            error: "/auth/error"
                        },
                        events: {
                            async linkAccount({ user }) {
                                await db.user.update({
                                    where: {
                                        id: user.id,
                                    },
                                    data: {
                                        emailVerified: new Date(),
                                    }
                                })
                            }
                        },
                        callbacks: {
                            async signIn({ user, account }) {
                    
                                if (account?.provider !== "credentials") {
                                    return true;
                                }
                    
                                if (!user.id) {
                                    return false;
                                }
                                const existingUser = await getUserById(user.id);
                    
                                if (!existingUser || !existingUser.emailVerified) {
                                    return false;
                                }
                    
                                return true;
                            },
                            async session({ token, session }) {
                                if (token?.sub && session?.user) {
                                    session.user.id = token.sub;
                                }
                                if (token?.role && session?.user) {
                                    session.user.role = token.role;
                                }
                                if (session?.user) {
                                    session.user.email = token.email;
                                    session.user.isOAuth = token.isOAuth;
                                }
                                return session;
                            },
                            async jwt({ token }) {
                                if (!token.sub) {
                                    return token;
                                }
                                const existingUser = await getUserById(token.sub);
                                if (!existingUser) {
                                    return token;
                                }
                                const existingAccount = await getAccountByUserId(existingUser.id);
                                token.isOAuth = !!existingAccount;
                                token.email = existingUser.email;
                                return token;
                            }
                        },
                        adapter: PrismaAdapter(db),
                        session: { strategy: "jwt" },
                        ...authConfig
                    });
                    ```
                    
            - Then in `app/api` directory of the client folder add a route group `auth/[...nextauth]/route.ts`
                
                ```tsx
                export { GET, POST } from "@/auth";
                ```
                
                This route will catch all authentication related requests.
                
            - After this we will create server actions for `login` , `register` and `verify-email` that will be used from the corresponding pages on the client.
        - 1.3.3.3: Handle user related routes
            - Route to send proofs to the blockchain: `api/vote`
                
                ```tsx
                import { NextResponse } from "next/server";
                
                const PRIVATE_KEY = process.env.PRIVATE_KEY;
                const ALCHEMY_API_KEY = process.env.ALCHEMY_API_KEY;
                const contractAddress = process.env.ONEVOTE_ADDRESS;
                
                export async function POST(req:Request){
                    try{
                		 const { vote, merkleTreeRoot, nullifierHash, proof,electionId} = req.json();  
                	    
                     const provider = new providers.AlchemyProvider(ALCHEMY_API_KEY);
                	   const signer = new Wallet(PRIVATE_KEY,provider);
                	   const contract = new Contract(contractAddress,oneVote.abi,signer);
                		  
                     const transaction = await contract.vote(vote,nullifierHash,electionId,merkleTreeRoot,proof);
                	   await transaction.wait();
                	   return new NextResponse("Success",{status:200})
                	     
                    }catch(error){
                        console.log('[ERROR_WHILE_SUBMITTING_VOTE_TO_BLOCKCHAIN]: ',error);
                        return new NextResponse("Server Error",{status:500})
                    }
                }
                ```
                
            - Similarly we can create the following routes:
                - Route to add connect a wallet to user account `api/user/add-wallet`
                - Route to add election: `api/election/new`
                - Route to add candidate to the election: `api/election/[electionId]/add-candidate`
                - Route to register as a voter to the election: `api/election/[electionId]/register`
                - Route to subscribe  to get results of the election when declared  `api/elections/[electionId]/notify`
                - Route to approve a voter for election: `api/elections/[electionId]/approve`
    - 1.3.4 Smart Contract Interactions
        
        On the smart contract we will need to:
        
        <aside>
        💡 For sake of **explanation** we are considering OneVote.sol as an entry point contract and  VotingProcess.sol as the election contract, after integration with all the other election contracts which are implemented with diamond proxy, the functions we are modify will change.
        
        </aside>
        
        <aside>
        💡 We are using contracts provided with semaphore SDK: these are set of opensource utility smart contracts, which are well audited making them appropriately **reliable**.
        
        </aside>
        
        1. Add a state variable to hold the address of our semaphore smart contract.
            
            ```tsx
            ISemaphore public immutable semaphore;
            ```
            
        2. Modify the constructor to save Semaphore contract address on deployment.
            
            ```tsx
            constructor(ISemaphore _semaphore) {
                    semaphore = ISemaphore(_semaphore);          
              }
            ```
            
        3. Create election: while creating the election proxy contract, we will create new semaphore group on-chain.
            
            ```tsx
             function createVotingProcess(
                    string memory _name,
                    string memory _description,
                    uint _startDate,
                    uint _endDate,
                    uint256 _groupId
                ) public {
                    address _votingProcessAdmin = msg.sender;
                    processCounter += 1;
                    VotingProcess vp = new VotingProcess(
                        processCounter,
                        _name,
                        _description,
                        _startDate,
                        _endDate
                    );
                    votingProcesses[processCounter] = vp;
                    votingProcessAdmins[vp] = _votingProcessAdmin;
                    semaphore.createGroup(_groupId, 32, address(msg.sender));
                }
            ```
            
        4. Add voter to election: we have to add the identity commitment of all the voters approved by the election admin.
            
            ```tsx
            function addVoter(uint256 identityCommitment) public {
                    semaphore.addMember(groupId, identityCommitment);
                    userAuthStatus[identityCommitment] = true;
            }
            ```
            
        5. Vote: This will have to be changed in order to first validate the ZK Proofs and only register the vote if the proof in valid.
            
            ```tsx
            function vote(
                    uint256 _vote, // candidate id.
                    uint256 nullifierHash,
                    uint256 pollId,
                    uint256 merkleTreeRoot,
                    uint256[8] calldata proof
                ) public {
                    semaphore.verifyProof(
                        groupId,
                        merkleTreeRoot,
                        _vote,
                        nullifierHash,
                        pollId,
                        proof
                    );
                    votingProcesses[pollId].vote(_vote);
                }
            
            ```
            
- **1.4 Thought Process and Design Decisions:**
    - Why do we need a backend when all the data can be directly stored and fetched from the blockchain?
        
        We need a backend because we cannot rely on the blockchain to fetch all the information needed to render our UI efficiently. By maintaining a database to store necessary information, we can efficiently implement logic to protect routes on the frontend and handle user authentication using email addresses. This way, we do not need to fetch the user authentication status for every single user.
        
        Maintaining a separate backend also allows us to abstract the blockchain wherever necessary, resulting in a more native experience for a large portion of internet users who are not familiar with web3.
        
    - Why not directly submit proofs from the client to the blockchain?
        1. Blockchain abstraction: If we send the proofs directly to the blockchain, it will require users to have a wallet and interact with the blockchain. This user experience will alienate many potential users who are not familiar with blockchain technology.
        2. User anonymity is compromised: Even if we choose to make connecting a wallet necessary for voters and have them make the vote call from the client, the transaction can still be linked to their wallet address, which is exactly what we are trying to avoid with our anonymous voting flow. Using the backend as a middle layer to send proofs, we use a common private key to send proofs for all users. This way, the transaction will only be linked to the common wallet and not the user, resulting in true user anonymity.
    - Why did I choose to exclude user KYC and have the election admin approve users manually?
        1. The scope of our application should initially be small organizations, communities, etc. Here, the verification requirements can vary greatly for different types of users, and this cannot be anyways covered by basic KYC all the time.
        2. If we consider implementing KYC we should keep it separate from our Agora Blockchain application to avoid doing multiple things in one repository and to keep reusability in mind.
        3. For automation of voter approval, we can later provide a single API to election admins, using which they could add checks and have all applications processed automatically using defined rules. Developing this is a significant task, so I put it out of scope to focus on implementing a good base first, but we can surely add it to the TODO list for our project.
    - How are transaction costs for making a vote call compensated?
        
        I have given it some thought, and I believe the cost of conducting an election should be borne by the election organizer. We can make them pay while making the createElection call itself by adding one extra require statement inside the createElection function.
        
        ```solidity
        require(msg.value>=/*The cost for conducting the election*/,"Please send ETH to cover the cost of conducting the election);
        ```
        
        However, I did not specifically mention it because, during development, we are using test tokens anyway. We can later discuss the cost with the mentor and organization admin and add that functionality accordingly.
        

## 2. Election Subscription and Voter Notifications

- 2.1 **Motivation and Goals**
    
    Keeping users informed throughout the election process is important factor for good user experience. By introducing an election subscription and notification system, voters can stay updated on the status of elections they are interested in, receiving timely notifications when results are available. This feature aims to improve the overall user experience and increase participation by keeping them informed without the need for constant manual checking.
    
    The primary goals of this feature are:
    
    - Allow voters to subscribe to elections of their choice.
    - Notify subscribed voters when election results are calculated and available.
- 2.2  **Flow**
    1. **Election Creation**: When an election is created, the election details including the end date/time are stored in the database.
    2. **Subscription**: Voters can subscribe to elections they are interested in through the UI.
    3. **Cron Job**: A single cron job is set up to run at a frequent interval, such as every minute. This cron job does the following:
        1. Queries the database to check for any elections that have ended but have not had their results calculated yet.
        2. For each ended election found:
            - Calls the **`calculateResult`** function on the smart contract to calculate the results.
            - Updates the election status and results in the database.
            - Retrieves the list of subscribed voters for that election from the database.
            - Sends email notifications to the subscribed voters using nodemailer, informing them that the election has ended and the results are available.
    4. **View Results**:  Users can view the election results by clicking on a link in the email notification, which takes them to the election page showing the results and stats.
        
        ![notification flow (1).png](assets/notification_flow_(1).png)
        
- 2.3 Technical Implementation
    - 2.3.1 Backend Subscription Management
        - Create a `vercel.json`  at the root of client
            
            ```json
            {
              "crons": [
                {
                  "path": "/api/cron",
                  "schedule": "* */10 * * *"
                }
              ]
            }
            ```
            
        - Create a new files `emails.ts` here we will have to setup nodemailer related config
            1. Setup transporter
                
                ```tsx
                import nodemailer from "nodemailer";
                
                const transporter = nodemailer.createTransport({
                  service: 'gmail',
                  host: 'stmp.gmail.com',
                  port: 587,
                  secure: false,
                  auth: {
                    user: process.env.SENDER_EMAIL,
                    pass: process.env.SENDER_EMAIL_PASSWORD
                  },
                });
                
                ```
                
            2. Create a function to send email
                
                ```solidity
                export async function sendMail(emailContent: EmailContent, sendTo: string[]) {
                  if (process.env.SENDER_EMAIL) {
                    const mailOptions = {
                      from: {
                        name: 'Agora Blockchain',
                        address: process.env.SENDER_EMAIL as string,
                      },
                      to: sendTo,
                      html: `<h1>Results for the election ${emailContent.electionName} are now available, click here ${emailContent.link}</h1>`;
                      subject: 'Results Declared'
                    };
                
                    transporter.sendMail(mailOptions, (err: any, info: any) => {
                      if (err) {
                        return console.log('[SEND_EMAIL]: Error encountered during sending the email: ', err);
                      }
                      console.log('[SEND_EMAIL]: Success ', info);
                    })
                  } else {
                    console.log('[SEND_EMAIL]: Env key not set correctly.');
                  }
                }
                ```
                
        - Query for all the elections whose duration has ended but the results have not yet been calculated.
            
            ```tsx
            function getAllPendingElections(){
            	const endedElections = await prisma.election.findMany({
              where: {
                end_time: {
                  lt: new Date(), // Filter elections whose end time is less than the current date/time
                },
                result: null, // Filter elections where the result is null (not calculated yet)
              },
              include: {
                subscribed_users: true, // Include the subscribed users for each election
              },
            });
            return endedElections;
            }
            ```
            
        - Create a function where using a for loop to call the smart contract to calculate results, update the election details on the database, fetch the subscribed voters and send them emails
            
            ```tsx
            async function processEndedElections(endedElections: Election[]) {
              for (const election of endedElections) {
                try {
            	    // This will be a function which calls the smart contract
                  const winningCandidateId = await calculateElectionResults(election.contract_address);
            
                  await prisma.election.update({
                    where: {
                      election_id: election.election_id,
                    },
                    data: {
                      status: 'COMPLETED',
                      result: winningCandidateId,
                    },
                  });
            
                  const subscribedUsers = election.subscribed_users;
                  const emailContent = {
            		      electionName:election.title
            		      link: `https://agorablockchain.com/elections/${election.id}`;
                  }
                  for (const user of subscribedUsers) {
            			      await sendMail(emailContent, user.email);
                  }
                  console.log(`Election ${election.election_id} processed successfully.`);
                } catch (error) {
                  console.error(`Error processing election ${election.election_id}:`, error);
                }
              }
            }
            ```
            
        - Create a new API route `api/cron/route.ts`
            
            ```tsx
            export async function POST() {
            	const pendingElections = await getAllPendingElections();
            	await processEndedElections(pendingElections);
              return Response.json({ status: "Success" });
            }
            ```
            
    - 2.3.3 **Frontend Subscription UI**
        
        ![Screenshot 2024-03-25 165816.png](assets/Screenshot_2024-03-25_165816.png)
        
        When the confirm button is clicked we will call the backend route `api/elections/[electionId]/notify` .
        

## 3. Code Refactoring

- 3.1 **Motivation and Goals**
- 3.2 **Current Codebase Structure**
- 3.3 **Proposed Refactoring**
    - 3.3.1 Migration to Next.js/TypeScript (include code snippets comparing old and new)
    - 3.3.2 Folder Structure Reorganization (include a tree diagram of new structure)
    - 3.3.3 Updating Deprecated Client Libraries
- 3.4 **Thought Process and  Benefits of Refactoring**

## 4. Integrating Anonymous Voting with Multiple Algorithms

- 4.1 Motivation and Goals
- 4.2 Current State of Anonymous Voting
- 4.3 Proposed Integration
    - 4.3.1 Extending Smart Contracts (include code snippets)
    - 4.3.2 Integrating with Diamond Multi-Facet Proxy (include a diagram)
- 4.4 Smart Contract Testing
    - 4.4.1 Unit Testing (include sample test cases)

## 5. Adding New Election Types

- 5.1 Motivation and Goals
- 5.2 Proposed New Election Types
    - 5.2.1 Approval Voting
        - 5.2.1.1 Algorithm Overview (include pseudocode)
        - 5.2.1.2 Smart Contract Implementation
        - 5.2.1.3 Frontend Integration
    - 5.2.2 Quadratic Voting
        - 5.2.2.1 Algorithm Overview (include mathematical formulas)
        - 5.2.2.2 Smart Contract Implementation
        - 5.2.2.3 Frontend Integration

# Outcomes

- 1 Project Impact
- 2 Future Scope and Maintainability

### 

- 2.4 Thought Process and Design Decision
    
    Why use cron jobs?
